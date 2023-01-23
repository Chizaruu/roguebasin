# Cluster chaining name generator

---

The name generator described here is one of those that scan an input text and generate names made of parts of the scanned words. The parts used in this case are tokens consisting of a vowel cluster and a consonant cluster, in either order. The beginning and the end of the words are treated as special, additional vowels denoted '<' and '>'. For example. "Elizabeth" would be tokenized <el-li-iz-za-ab-be-eth-th>.

After scanning and tokenizing all input words, random names are generated as follows: the first token is chosen randomly from the set of tokens starting with '<'. Each subsequent token is randomly chosen from tokens starting with a cluster matching the last cluster of the previous one. The matching token is attached to the already generated part of the word (of course, the matching cluster is not repeated), and the loop is continued until the minimum required number of tokens is accumulated. The last token is subject to an additional constraint: it must end with a '>'. The total number of tokens in the generated name must be even, as the word ends are both treated as vowels. The random selection takes into account the number of occurrences of the given token in the input file; the probability that the given matching token is selected is proportional to this number.

A sample of names (4 to 6 tokens long) generated from a list of 101 English female names: Mabet Sarope Failylla Math Pebetia Blance Harilda Chridgera Reron Alenriet Melia Jahella Ademaudye Elliane Edorcar Chetia Hence Annitha Floruda Alenet

It may be advantageous to put additional constraints on the output of the generator, for example, requiring minimum and maximum number of letters, excluding names that are the same as input words or contain words from some additional input list etc.

A slight modification of the algorithm produces names with endings more similar to those in the input list. In this variant, a lone '>' is not regarded as an ending vowel suffix, but appended to the previous token, thus, for example, <el-li-iz-za-ab-be-eth>. A C implementation of this version is available as "y_names23.c" [here](http://kusigrosz.jimdo.com/roguelike-related).

A variant of this algorithm using three-cluster tokens of the form vowels-consonants-vowels (e.g. <eli-iza-abe-eth>) generates names that sound more similar in character to the input ones, at the cost of offering less variety.
