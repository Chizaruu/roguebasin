# Markov chains-based name generation

---

Markov-Chains are usually used in the field of thermodynamics, but I will be using a fudged version of Markov-Chains to generate some random names. The basic premise of Markov-Chains is that if we have a big enough data set we should be able to predict the next event in a series of seemingly random events. So using this we need to create a dataset of names that we want to generate, lucky for you I have already done this. Here is the [List of Names](list_of_names.md).

This list is every male and gender-neutral name from babynames.com. Now we need to create a program to parse this list for it's statistics. To make this interesting I have split the statistics up, 1. Letter combinations that begin names, 2. Letter combinations that end names, and 3. Letter combinations that are in between the beginning and end. This should give us slightly better names. The following is my code samples in C++, after compilation this program will output some randomly generated names.

---

```cpp
// CWordFrequency.h
#ifndef CWORDFREQUENCY_H_
#define CWORDFREQUENCY_H_

class CWordFrequency
{

   private:
       int countBeginning;
       int countEnd;
       int countWithin;

   public:
       CWordFrequency();
       ~CWordFrequency();
       void incrementCountBeginning();
       void incrementCountEnd();
       void incrementCountWithin();
       int returnCountBeginning();
       int returnCountEnd();
       int returnCountWithin();

};

#endif
```

---

```cpp
//CWordFrequency.cpp
#include "CWordFrequency.h"

CWordFrequency::CWordFrequency() : countBeginning(0), countEnd(0),
countWithin(0)
{

}

CWordFrequency::~CWordFrequency()
{

}

void CWordFrequency::incrementCountBeginning()
{
    ++countBeginning;
}

void CWordFrequency::incrementCountEnd()
{
    ++countEnd;
}

void CWordFrequency::incrementCountWithin()
{
    ++countWithin;
}

int CWordFrequency::returnCountBeginning()
{
    return countBeginning;
}

int CWordFrequency::returnCountEnd()
{
    return countEnd;
}

int CWordFrequency::returnCountWithin()
{
    return countWithin;
}
```

---

```cpp
//CRandomName.h
#include <fstream>
#include <map>
#include <string>
#include <cstdlib>
#include <ctime>
#include <vector>
#include <algorithm>

#include "CWordFrequency.h"

#ifndef CRANDOMNAME_H_
#define CRANDOMNAME_H_

class CRandomName
{

    private:

        std::string errorMessage;
        std::ifstream *fileStreamIn;
        std::ofstream *fileStreamOut;
        std::map<char, std::map<char, CWordFrequency> > baseMap;
        std::map<char, CWordFrequency> sequenceFrequencyMap;
        std::vector<char> startChars;
        std::vector<char> availableChars;
        CWordFrequency tempFrequency;

    public:

        CRandomName();
        ~CRandomName();
        void inputFile(std::ifstream &streamHandle);
        void processFile();
        void outputList(std::ofstream &streamHandle);
        std::string outputName(double minLength, double maxLength);

};

#endif
```

---

```cpp
//CRandomName.cpp
#include <iostream>
#include <string>
#include <set>
#include <iterator>
#include "CRandomName.h"
using namespace std;

CRandomName::CRandomName()
{
    srand(time(NULL));
}

CRandomName::~CRandomName()
{
    fileStreamOut->close();
    fileStreamIn->close();
}

void CRandomName::inputFile(std::ifstream &streamHandle)
{
    fileStreamIn = &streamHandle;
}

//Set the boolean to true if you have a file that contains names with whitespaces. This does mean that the
//end of each name should be marked by a end of line character.
//Also the words will be converted to uppercase automatically
void CRandomName::processFile(bool NoWhiteSpaceSkip)
{

    std::string word;
    std::set<char> startCharsSet; //these sets are used to list the different starting and available chars.
    std::set<char> availableCharsSet; //they will be converted to vectors later.
                                       //I needed sets for their key ability, and vectors later because they
                                       //offer better access to the elements contained in the vector.
    char base;
    char sequence;
    int wordPosition;

    startChars.clear();
    availableChars.clear();

    while(fileStreamIn->good())
    {
       //Ignore whitespaces between words. Different names should be seperated by a '\n'
       if(NoWhiteSpaceSkip) {
          getline(*fileStreamIn,word);
       } else
           *fileStreamIn >> word;

        if(word.length() > 1)
        {
           startCharsSet.insert(word[0]);
           availableCharsSet.insert(word[0]);

           for (wordPosition = 0; (wordPosition + 1) < (word.length()); wordPosition++)
           {
              availableCharsSet.insert(word[wordPosition+1]);
              base = word[wordPosition];
              sequence = word[wordPosition + 1];

              CWordFrequency &wf = baseMap[base][sequence];

              if (wordPosition == 0) {wf.incrementCountBeginning();}
              else if ((wordPosition + 1) >= (word.length() - 1)) {wf.incrementCountEnd();}
              else if ((wordPosition > 0) && ((wordPosition + 1) < (word.length() - 1))) {wf.incrementCountWithin();}
           }
        }
   }

   set<char>::iterator it;
   for ((it = startCharsSet.begin()); it != startCharsSet.end(); it++)
        startChars.push_back(*it);
   for ((it = availableCharsSet.begin()); it != availableCharsSet.end(); it++)
        availableChars.push_back(*it);
}

void CRandomName::outputList(std::ofstream &streamHandle)
{
   fileStreamOut = &streamHandle;

   std::map<char, std::map<char, CWordFrequency> >::iterator itr;

   std::map<char, CWordFrequency>::iterator itr2;

   for (itr = baseMap.begin(); itr != baseMap.end(); itr++)
   {
       sequenceFrequencyMap = itr->second;
       for (itr2 = sequenceFrequencyMap.begin(); itr2 != sequenceFrequencyMap.end(); itr2++)
       {
           tempFrequency = itr2->second;
           *fileStreamOut << itr->first << " " << itr2->first << " "
               << tempFrequency.returnCountBeginning() << " " <<
               tempFrequency.returnCountWithin() << " "
               << tempFrequency.returnCountEnd() << std::endl;
       }
   }
}

//Known bugs, if there are not enough names in the seed the program will crash.
std::string CRandomName::outputName(double minLength, double maxLength)
{
   std::string name;
   std::vector<char> freqVector;
   double range = static_cast<double>((maxLength - minLength) + 1);
   int rangeLength = static_cast<int>(minLength + (range * ((double)rand() / (double)(RAND_MAX + 1))));
   char a =  startChars.at(static_cast<int> (startChars.size() * rand() / ( RAND_MAX + 1.0 ) ));
   name += a;

   for(int counter = 1; counter < rangeLength; counter++)
   {
       int cdc = 0;
       if(baseMap.find(a) != baseMap.end())
       {
        for (int count = 0; count < availableChars.size(); count++)
        {
           char b = availableChars.at(count);
           if(baseMap[a].find(b) != baseMap[a].end())
           {
               if(counter == 1)
               {
                   for(int cc = 0; cc < (baseMap[a][b].returnCountBeginning()); cc++)
                   {
                       freqVector.push_back(b);
                       cdc++;
                   }
               }
               else if((counter + 1) >= (rangeLength - 1))
               {
                   for(int cc = 0; cc < baseMap[a][b].returnCountEnd();cc++)
                   {
                       freqVector.push_back(b);
                       cdc++;
                   }
               }
               else
               {
                   for(int cc = 0; cc < baseMap[a][b].returnCountWithin();cc++)
                   {
                       freqVector.push_back(b);
                       cdc++;
                   }
               }
           }
       }
   }
       std::random_shuffle(freqVector.begin(), freqVector.end());
       std::random_shuffle(freqVector.begin(), freqVector.end());
       std::random_shuffle(freqVector.begin(), freqVector.end());
       int c = (int)(((cdc) * rand() / ( RAND_MAX + 1.0 )));
       name += freqVector.at(c);
       a = freqVector.at(c);
   }
   return name;
}
```

---

```cpp
//main.cpp
#include <iostream>
#include "CRandomName.h"

int main()
{
   CRandomName name;

   std::ifstream inFile("NameList.txt");
   std::ofstream outFile("Stats.txt", std::ios_base::trunc);

   name.inputFile(inFile);
   name.processFile();
   name.outputList(outFile);

   std::cout << name.outputName(3, 9) << '\n' <<
       name.outputName(3, 9) << '\n' <<
       name.outputName(3, 9) << '\n' <<
       name.outputName(3, 9) << '\n' <<
       name.outputName(3, 9) << '\n';
}
```

(The code is public domain)

And here is some sample output:

```text
ONMY
TERLEMDA
ZENEEVML
CHIA
SAWH
SPTKIVCDE
ILAN
COONR
KETADAN
VIAN
XAIIN
URRIRERON
FRTOKRR
YONNAN
IDANSZEDS
CAALLSTF
FLVAHE
XHAM
DUSEXNCST
DEBAD
SHEUO
WARFA
MUASS
INEE
```

As you can see, some are ok but most aren't. This is because the dataset although big isn't quite big enough. To compensate for this, you could try to implement artificial rules to make the names more readable, but I will leave that as an exercise for the reader.
