# Names from a high order Markov Process and a simplified Katz back-off scheme

---

Various articles on RogueBasin have suggested that a Markov Process might be a good way to generate random names for a game. However, most of them have a weakness or two which can easily be solved. In this article, I will describe a basic first order Markov process seen in other articles on name generation, and discuss some of the short comings. I will then explain how to overcome these weakness using a higher order model, with a simplified Katz back-off scheme. This model is still fairly simple, but will allow you to come up with a data based name generator, even with a somewhat limited amount of data, which will produce pronounceable names with a similar feel to your training data.

## Preliminaries (you can skip if you already know a bit about Markov processes)

---

### Markov Process

A Markov model can be thought of as a generative model of data. In other words, it defines a full joint probability distribution which hypothesizes how the data was generated. However, this distribution has a very special property know as the Markov property. Essentially what this property says is that the output of the Markov process as any particular point in the output sequence depends entirely upon a fixed window of previous outputs. In other words, the following holds:

p(s*i | s*{i-1}, s*{i-2}, ..., s_0) = p(s_i | s*{i-1})

This equation gives the equation for a first order Markov model. Essentially what this says is that the current output depends solely upon the previous output. In the context of name generation, this might means that I will output a particular character based on the previous character. For example, if I just generated a 'z', I would be unlikely to generate another 'z', but would likely generate a vowel. The only trick then becomes coming up with the exact transition probabilities so I can ask things like what is the probability of generating an 'e' following a 'z'. It turns out that we do a simple maximum likelihood estimate from data by simply looking at all the times we observed 'ze', divided by all of the times that we say a 'z'.

### Higher Order Models

In general though, a first order model might not be sufficient. It turns out that if we increase the size of the window we look back in time, we often get better looking outputs. This is unsurprising when we consider that many phonemes require more than one letter to express, or that syllabic dependencies might make a word unpronounceable. In order to have a higher order Markov model, we simply increase the size of our context from 1, to a bigger number. For example for an order 3 model the following would hold under the Markov property:

p(s*i | s*{i-1}, s*{i-2}, ..., s_0) = p(s_i | s*{i-1}, s*{i-2}, s*{i-3})

Training this model is accomplished in the same way as before, only we have to store more context. Now instead of asking things like the probability of observing an 'e' after a 'z', we have ask for the probability of an 'e' followed by the previous n letters, where n is the order of our model. The same principal applies though to train the model. We simply count the number of times our context of length n was followed by an 'e', and divide by the number of times we observed that context. We can very easily store all of these probabilities an a lookup table.

## Problems with the Markov Process

---

### Data Sparsity

We often don't have enough data to properly train our models. This is especially true if we have a higher order model. In general, the amount of data needed to train a model increase exponentially with the order of our model. If we don't have enough data, then the models will produce lower quality names, and be unable to generate some things it ought to, simply because the model never observed a particular character sequence in training. We can solve this by giving a Dirichlet prior to our model, which will be explained in the next section.

### Zero Frequency

Another issue is that sometimes a higher order model will get stuck. It is possible to generate a sequence which leads to a context for which there was no training data. This is even worse than simply not having enough data to correctly assign probabilities as before, as we will not even have a table to perform a look up, which explains some of the crashes mentioned in other name generation articles on RogueBasin. We can solve this by employing a back-off scheme, which will be explained in the next section.

### Start and End

There is one slight hicup with the way we have setup the model. We want every character in our generated sequence to be based on the previous n characters in the sequence, which works fine if we are n characters into the sequence. For example, suppose my data consisted of only two names: 'abb', and 'acc'. If we do not model the beginning of the names correctly, we might end up generating names like 'bb' or 'cc', when we never actually saw a name begin this way. Similar issues exist with the end of names. This problem can be solved by prepending special start symbols to names in our training data, and by appending special end symbols to the end of names in our training. This will be explored more carefully in the next section.

## Revised Markov Process

---

### Dirichlet Prior

We can overcome the data sparsity issue to some degree by applying additive smoothing to all of our look up tables. This means that we simply add a constant to every count in our look up tables, even the spots where the count would have been 0. This constant can be any positive real number. For tables where we have very little data, the prior will smooth out the distributions so that we don't get too heavily swayed by a small number of observations. When we have a large number of observations, the prior will matter less since the counts are high enough to wash out that small constant. This also has the advantage that every probability will be non-zero. This is nice since it means we can generate any possible name, even if unlikely due to our training data.

In Bayesian terminology, this basically means we are adding a prior to the expected values from our distribution. Knowing what the Dirichlet distribution looks like might give you some insight into what this prior means. The general intuition is that a prior closer to 0 means you trust the data more than your prior. A value of 1 corresponds to an uninformative prior. Values greater than 1 will tend to smooth more and make your distributions more uniform.

### Katz Back-off

As mentioned, we can sometimes hit a problem when find ourselves trying to sample given a context we have never seen before. This is slightly different than the previous problem where we may have seen a context, but not often enough to have a good estimate of the probabilities. The zero-frequency problem is one where we have no table to even look up. Back-off schemes give a way out.

Essentially what we want to do is use a short context when ever we have no data at all. This means that when training an nth order model, we will simultaneously train up a n-1 order model, an n-2 order, and so on until a 0-order model. Then when ever we are given a context for which there is no data, we simply look up the probability in the next model of lower order.

This is slightly different than the original Katz back-off scheme, which gave a threshold for when you would backoff (in our case we just say that threshold is 0). Katz also gave a smoothing parameter which would be calculated using Good-Turing smoothing. While not to complicated, Good-Turing smoothing is probably beyond most people who have not studied natural language process more in depth than this tutorial is giving. For simplicity, we will just make this back-off weight 1, meaning we can ignore it.

### Start and End Symbols

In order to get correct probabilities for the beginning of the a name though, we need to prepend n special start symbols to every name in our training data, where n is the order of our model. This should be a symbol which is never used in the training data to avoid confusion. Then when we train the model, the context for the first character will actually be a sequence of start symbols.

Similarly, we should model the end of words too. We do this in the same way, by having a special end of name symbol. Here we only ever need to append one such symbol, regardless of the order of our model, since once we hit this symbol, we will not generate observe any more data in that sequence.

## Evaluation

---

So now we have a model we can implement and train with our data, but there are a few tunable parameters we need to tweak. The first is the order of model we need to use. If we set it too low we will miss syllabic dependencies in the data. If we set it too high, we will end up simply memorizing the data with our model and it will be difficult to generate original names (although as previously stated, the prior can help a great deal here). The other parameter we need to tune is the prior. Too low and we'll end up reproducing the data, and too high means we'll start to drift away from the pronoucability and feel of our data.

### Subjective Evaluation

There are two ways we can evaluate a particular set of parameters. First, the developer can simple choose some parameters, and then generate a bunch of names, and manually inspect the output to see if it matches the desired criteria. This isn't a terrible principled approach theoretically, but in practice it should work quite well for a game. In fact, it is the approach I would personally recommend that you take. However, if you are terribly curious, or really like machine learning and want to do it properly, then by all means read on! This was the approach I used in the example tests below.

### Likelihood of the Data

Another approach is to compute the likelihood of the data, or better yet, the likelihood of some held out data. Using our model, we can always evaluate the likelihood of an particular character in the name given the previous characters in the name (its just the ratio of the count of the character with the given context to all the other characters given the context). The likelihood of any particular name is simply the product of each of the characters of the name (including start and end symbols). If we want to compute the likelihood of an entire dataset, we simply multiply the likelihood of generating each name in the dataset.

Now that we have a way to compute likelihood of the data, we simply partition our dataset into two, one for training, the other for test. Note that the partitions don't have to be half and half (the training data is commonly about 80% of the data). We train a whole bunch of models with different parameter settings. We will try different orders, and different priors, and every combination of the two that we suspect might yield a good model. We then evaluate the models by computing the likelihood of the data for the test data. Assuming that the two datasets are reasonable similar, the model which gives the highest likelihood to the held out test data is the best.

Of course it is entirely possible that the particular partition we came up with was biased in some way, so it is also common to do what is called cross-fold validation. This means we will partition our data into a n (commonly 10) different partitions. For each parameter setting, we will build n models, each with a different partition withheld as test data. For a given parameter configuration, we then average the n likelihoods into a single number. This practice makes it much less likely that we will select a suboptimal model based on the bias of a particular partitioning scheme. Once again, after performing these evaluations, we select the parameter setting which maximized the likelihood of the data.

Once we know what parameter setting we want to use, it is appropriate to train our final model on the entire dataset, with nothing withheld. We will have tested various parameter settings to find the best possible setting, but in the end we want to use all the data to give our model as much information as possible.

### Log Space

One final and important note: these calculations should be performed in log space! The reason is that we will be multiplying hundreds if not thousands of small numbers together, which goes well beyond the precision of floating point numbers. Instead, each time you calculate the probability of a particular character, take the log of the probability, and use the log probability instead. You may (or may not) recall your log rules - just remember that in log space, multiplying two number together is done with addition (ie log a \* b = log a + log b). This means that the log probability of a name is the sum (not product) of the log probabilities of each character in the name. The log probability of a dataset is the sum (not product) of the log probabilities of each name in the dataset. You will end up comparing very large negative numbers instead as log 0 = -infinity and log 1 = 0. As before, larger log likelihoods are better.

## Test

---

After implementing the model, I wanted to test it using various types of data to demonstrate that the model does in fact produce pronounceable names which fit the general feel of the training data. I will be testing on names from Morrowind, names from Tolkien, and some common baby names. For all the datasets, I used a prior of .001, and an order of 3. Some names where generated which where present in the data, but these names where removed from the output.

### Morrowind

I used all of the male and female [Dunmer names](http://www.uesp.net/wiki/Lore:Dunmer_Names) from Morrowind. The following is a list of names generated by the model.

Bral Methyn Falure Ilmenarara Bildrenziah Felvon Tarvyne Ivulis Veralosa Farak Tadeli Fevis Folsa Vavelasa Brelamu Suneth Garaldrala Trevese Galosa Idros Dreyne Trilu Broder Savonar Derama Tarvyn Favel Galis Sunela Mandraren Donu Brelisi Sarval Bralds Gavisa Dolmerereralam Feryra Dovora Sathal Garas Milveni Nethasa Arnsu Varvs Arver Narera Uraden Serer Elynea Feli Vavas Alvonu Mertisa Rels Draras Tolven Mival Vatollia Neldyne Ethal Gindas Iveri Helso Llival Beveli Salar Nethas Llaner Dalnor Rila Delmenisea Trele Rathasa Alveri Nels Nathis Rupse DralinNilvam MalUvren Vireri Selvuren Favener Dolvam Brelyni Daynaso Nurisea Salam Ervamea Ane Salyne Rovor Galtiiras Favasi Drinar Llavas Brelayni Direnziah Madur Brone Medilenasie

### Tolkien

Using the following [list](http://en.wikipedia.org/wiki/List_of_Middle-earth_characters) of names taken from Lord of the Rings trilogy, I trained yet another model. Note that I removed any notes, titles, prefixes or suffixes from the data as a preprocessing step.

Orodda SarWill Galand Teleg Ancalin Erendomiel Palas Deór Sirion Arth Grishnárion Calemmakil Calimeldë Aiwen Gildeson Celeg Ad?n Glauredhelion ElfheloOlwë Finvassion Glanor Eldar Shel GhFinrodredegar Faladin Handar Malandacil Olóin Calingolodh Duil Arant Turambaval Adra?riel Elendacarion Antar Rúmendir Mahtanatar Goth Mahtar Lagdush Npher Meriën Thor Bryrbur Belethoregon Faranc Dernhee Aragon Aerië Borondomiel Argot Aravann-bur Hirgothain Fingollum Sakalthôn Herungon Imras Deôn Lobel Erador Fanimeldur Aiw?in Bellador Gwine Eldarin Ardamir Ilúk Ardang Thróin Telcharn Farach Hyaar Magol Fengeleg Zimroth Sm?lfwine Hirion Nessar Zimroth LothmoBaldad Handar Sarumor Maggothmog Wal?rendis Treebeardang Gormadowfaxurín Magginsilmo Mahtar Elfwingol Eärnu?rnil Cast Barandis Mu?ma Tindur Anfaug Dorlimon Sadowfax Malbo Erestoher Déod

### Baby Names

Using the top 500 [baby names](http://www.ssa.gov/cgi-bin/popularnames.cgi) (both male and female) for 2010, I trained one final model. The following is a list of those generated names.

Cecille Marius Hadler Mohance Valena Erinica Mohamer Lilas Landriana Kayliett Nathance Jaelan Grego Rylina Milana Edwardo Rosephen Kathew Jaide Vincoln Georgan Hanna Oma Calie Arabeth Jeffred Fernandyn Harmondy Davierce Penesiree Ethaniyah Malla Mika Jadelanie Valey Lawrenis Hunte Kateo Gaelynn Cynthony Brennikaelyn Lince Bryana Dust IUriel Jonard Chasey Nadisy Kenna Diegory Jaimena Cequinn Laily Marionahim Carsha Melaina Brael Ismaelyn Estrelly Micardo Lincolas OmaQuinton Arthur Lucilla Kellas Larrentin Melodh Kaitlina Makenzo Tatian Ismaelyn Maxwelle Moisella Tris Sier Mollys Luca Haydon Viviana Addisy Connedy Colter Maurick RoberlyDari Damondon Quenton Valey Elison Nayelicia Cathanna Summeron Ales Randa Arian Dayan Elison Lela Collyssando Jaley Micholas Valeb Madalla

### Implementation

The following is my quick and dirty implementation of the previously discussed model. It may not be the prettiest, but it seems to get the job done.

```python
from **future** import division
import random

class Categorical(object):

    def __init__(self, support, prior):
        self.counts = {x: prior for x in support}
        self.total = sum(self.counts.itervalues())

    def observe(self, event, count=1):
        self.counts[event] += count
        self.total += count

    def sample(self, dice=random):
        sample = dice.uniform(0, self.total)
        for event, count in self.counts.iteritems():
            if sample <= count:
                return event
            sample -= count

    def __getitem__(self, event):
        return self.counts[event] / self.total

class MarkovModel(object):

    def __init__(self, support, order, prior, boundary_symbol=None):
        self.support = set(support)
        self.support.add(boundary_symbol)
        self.order = order
        self.prior = prior
        self.boundary = boundary_symbol
        self.prefix = [self.boundary] * self.order
        self.postfix = [self.boundary]
        self.counts = {}

    def _categorical(self, context):
        if context not in self.counts:
            self.counts[context] = Categorical(self.support, self.prior)
        return self.counts[context]

    def _backoff(self, context):
        context = tuple(context)
        if len(context) > self.order:
            context = context[-self.order:]
        elif len(context) < self.order:
            context = (self.boundary,) * (self.order - len(context)) + context

        while context not in self.counts and len(context) > 0:
            context = context[1:]
        return context

    def observe(self, sequence, count=1):
        sequence = self.prefix + list(sequence) + self.postfix
        for i in range(self.order, len(sequence)):
            context = tuple(sequence[i - self.order:i])
            event = sequence[i]
            for j in range(len(context) + 1):
                self._categorical(context[j:]).observe(event, count)

    def sample(self, context):
        context = self._backoff(context)
        return self._categorical(context).sample()

    def generate(self):
        sequence = [self.sample(self.prefix)]
        while sequence[-1] != self.boundary:
            sequence.append(self.sample(sequence))
        return sequence[:-1]

    def __getitem__(self, condition):
        event = condition.start
        context = self._backoff(condition.stop)
        return self._categorial(context)[event]

class NameGenerator(object):

    def __init__(self, name_file, order=3, prior=.001):
        names = set()
        support = set()
        for name in name_file:
            name = name.strip()
            if len(name) > 0:
                names.add(name)
                support.update(name)
        self.model = MarkovModel(support, order, prior)
        for name in names:
            self.model.observe(name)

    def generate(self):
        return ''.join(self.model.generate())
```
