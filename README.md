# Code--
# -*- coding in python -*-

#import all library

import bs4 as bs
import urllib.request
import nltk
import nltk
nltk.download('punkt')
nltk.download('stopwords')
import urllib.request
import re
import pandas as pd 
import heapq

# Gettings the data source
#https://en.wikipedia.org/wiki/Cancer
source = urllib.request.urlopen('https://en.wikipedia.org/wiki/Cancer').read()
source

# Parsing the data/ creating BeautifulSoup object
soup = bs.BeautifulSoup(source,'lxml')
soup


# Fetching the data
text = ""
for paragraph in soup.find_all('p'):
    print(paragraph.text)
    text += paragraph.text 
    text += '\n\n'

# Preprocessing the data
# The re.sub() function in the re module can be used to replace substrings. 
text = re.sub(r'\[[0-9]*\]',' ',text)
text = re.sub(r'\s+',' ',text)
#print(text)


# Cleaning text
#\d, \w and \s --- Shorthand character classes matching digits, word characters, 
#and whitespace. Can be used inside and outside character classes.

clean_text = text.lower()
clean_text = re.sub(r'\W',' ',clean_text)
clean_text = re.sub(r'\d',' ',clean_text)
clean_text = re.sub(r'\s+',' ',clean_text)
clean_text


# Tokenize sentences
sentences = nltk.sent_tokenize(text)

# Stopword list
stop_words = nltk.corpus.stopwords.words('english')
print(stop_words)


# Word counts 
word2count = {}
for word in nltk.word_tokenize(clean_text):
    if word not in stop_words:
        if word not in word2count.keys():
            word2count[word] = 1
        else:
            word2count[word] += 1

#checking word count 
print(word2count)


# Converting counts to weights
for key in word2count.keys():
    word2count[key] = word2count[key]/max(word2count.values())
print(word2count)


# Product sentence scores    
sent2score = {}
for sentence in sentences:
    for word in nltk.word_tokenize(sentence.lower()):
        if word in word2count.keys():
            if len(sentence.split(' ')) < 25:
                if sentence not in sent2score.keys():
                    sent2score[sentence] = word2count[word]
                else:
                    sent2score[sentence] += word2count[word]


  
print(sent2score)

# Gettings best 5 lines             
best_sentences = heapq.nlargest(5, sent2score, key=sent2score.get)

print('---------------------------------------------------------')
for sentence in best_sentences:
    print(sentence)
