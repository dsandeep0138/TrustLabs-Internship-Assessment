# Trust Labs Internship Challenge

## Packages
1. Text analysis tools such as spacy in NLP for keyword detection.
2. Text mining packages such as BeautifulSoup
3. Relation Extraction packages such as Reverb, Ollie or OpenNLP by Stanford.
4. Machine Learning tools (which include Numpy, Pandas, Networkx)

## Languages
Python

## Running code
The notebook is already run and uploaded. The notebook can be re-run by running all the cells again.

## Sub-problems
The following sub-problem is mainly focused upon. However, To suggest an end-to-end model, I have discussed the other parts or sub-problems as well. More details below: 

```
Create a scalable data store to store misinformation and hate speech content. 
Considerations: What kind of data store to use? What format/schema to store 
data in so we can easily match against other other content from social media
platforms? How to make the system efficient in terms of storage and/or performance?
```

Outline:
```
Create a scalable data store to store misinformation and hate speech content. 
Considerations: What kind of data store to use?
```

Described and provided a proof-of-concept approach to store the misinformation and the actual facts in the form of the related keywords. All the relations between the keywords are stored in the data store instead of the actual article which makes it scalable and store only the required information.

```
What format/schema to store 
data in so we can easily match against other other content from social media
platforms?
```

The data is stored in the form of (keywordA, relation, keywordB, confidence) tuples. Then, the articles and related information can be separately stored in the database. 

It is also easy to match it with other social media platforms since the text from any other social media platform can be processed in the similar way to get the relations and the truth score can be found out.

```
How to make the system efficient in terms of storage and/or performance?
```

This method of storing the relations is efficient in terms of performance since all the data need not be processed and the new information can be determined whether it is true or false based on the keywords from the article itself.

As part of this notebook, I describe the following and suggested an approach as a proof-of-concept using bits-and-pieces of python code.

## How to build high quality ground truth?
In the approach I described, we will maintain a trustrank (similar to pagerank in Google). Most trustworthy sources have higher trust rank and least trustworthy sources have lower rank. It can be determined by scraping the already identified facts (by politifact, for example) and checking the true articles per total articles ratio to identify the trustranks.

Based on articles from these higher trust rank sources, we could achieve the high quality ground truth.

## How the information can be efficiently stored
We already have a trustrank for the sources of information.

Next step to store the information is (as explained in the notebook) through Knowledge Graphs. For each information, main keywords are identified using NLP (Natural Language Processing) techniques. Then the relations between the keywords can be identified using the Relation Extraction Packages such as Reverb, Ollie or AllenNLP. For the proof-of-concept, this notebook takes up a simple co-occurrence as the relation existence criteria. For each relation extracted, there is a confidence value which denotes the confidence of finding the relation in the real news. And similarly, confidence value in the Fake Knowledge Graph denotes the confidence of the relation in the fake news.

For example, let below be the relations and their confidences:
("children", "affected", "coronavirus"): 0.85
("children", "got", "coronavirus"): 0.67

let below be the relations in Fake knowledge graph:
("children", ""affected", "coronavirus"): 0.02
("children", "exempted", "coronavirus"): 0.93

We could see that the news that the "children are affected by coronavirus" is mostly True since we see a higher confidence score in the Real KG and low confidence score in Fake KG. Similarly, "Children are exempt from coronavirus" is mostly False since the relation has high score in the Fake KG.

## Finally, how to identify if any new information is real or fake.

Finally, a scoring approach is defined to determine whether the new information is true or false. Scoring approach is based on the edge weights (confidence scores) in the real and fake KG. 

1. First step is to find out the relation pairs.
2. For each relation in the real Knowledge Graph, increase the score by 1 * (confidence of the veracity) i.e., edge-weight of the relation
3. For each relation in the fake Knowledge Graph, decrease the score by 1 * (confidence of the fakeness) i.e., edge-weight of the relation in the fake KG.
4. Normalize the value by dividing with the total number of relations (so that the score is between -1 and 1)
5. Get the article (or information) published site, get the trust rank (page rank) of the publisher.
6. Multiply the score with this trust rank.

Note that this takes into account the information such as trustrank (i.e., trustworthy of the source)

Algorithm for the Trustrank (trustworthy of the sources) can be improved by considering multiple features like:
1. Number of followers
2. Frequency of the information delivery
3. Number of verified correct articles (or percent of correctness)

## Final Steps and Feedback

None of the real and fake Knowledge Graphs are updated once the system figures out the truthness of the information, but instead the verdict can be posted to Twitter.

After a certain period of time (most likely a day), feedback on the verdict can be obtained through the number of likes/retweets of the verdict tweet or based on the general sentiment of the comments on this post.

If the verdict of the information is deemed true, then the relations are updated into the Knowledge Graph.
