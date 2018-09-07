# Amazon Review Sentiment Analysis Web

Pranav Eranki 2018

Flask web app that uses a API service to predict whether the product review is positive or negative.

# Dataset

[Amazon product data](http://jmcauley.ucsd.edu/data/amazon/links.html) was provided by Julian McAuley, UCSD.

We cover the sentiment analysis of 12 categories of Amazon products. We list our results in test datasets using NBSVM (Naïve Bayes - Support Vector Machine) inspired by [a Kaggle kernel](https://www.kaggle.com/jhoward/nb-svm-strong-linear-baseline-eda-0-052-lb).

NBSVM was introduced by Sida Wang and Chris Manning in the paper [Baselines and Bigrams: Simple, Good Sentiment and Topic Classiﬁcation](https://nlp.stanford.edu/pubs/sidaw12_simple_sentiment.pdf). Here we use sklearn's logistic regression, rather than SVM, although in practice the two are nearly identical (sklearn uses the liblinear library behind the scenes).

| Category                   | Precision (%) |
|----------------------------|--------------|
| Automotive                 | 89.29        |
| Baby                       | 90.28        |
| Clothing Shoes and Jewelry | 90.50        |
| Digital Music              | 88.92        |
| Electronics                | 91.58        |
| Grocery and Gourmet Food   | 90.33        |
| Home and Kitchen           | 92.05        |
| Kindle Store               | 92.57        |
| Pet Supplies               | 89.32        |
| Sports and Outdoors        | 91.19        |
| Toys and Games             | 91.24        |
| Video games                | 88.93        |

# Installation

To install the Python packages for the project, clone the repository and run:

`conda env create -f environment.yml`

from inside the cloned directory. This assumes that [Anaconda Python](https://www.continuum.io/downloads) is installed.

```
# Build the model
python build_model.py

# Run the App
python run.py
```

# Test App

Open Browser: http://localhost:5000.

Choose the category of your purchased product, fill in your own reviews and get results like the following:

<img src="https://github.com/wangruinju/Amazon_Review_Sentiment_Analysis/blob/master/Demo.png" width="600" />

# Data ETL

Format is one-review-per-line in (loose) json. See examples below for further help reading the data.

Sample review:

```
{
  "reviewerID": "A2SUAM1J3GNN3B",
  "asin": "0000013714",
  "reviewerName": "J. McDonald",
  "helpful": [2, 3],
  "reviewText": "I bought this for my husband who plays the piano.  He is having a wonderful time playing these old hymns.  The music  is at times hard to read because we think the book was published for singing from more than playing from.  Great purchase though!",
  "overall": 5.0,
  "summary": "Heavenly Highway Hymns",
  "unixReviewTime": 1252800000,
  "reviewTime": "09 13, 2009"
}
```
where

* reviewerID - ID of the reviewer, e.g. A2SUAM1J3GNN3B
* asin - ID of the product, e.g. 0000013714
* reviewerName - name of the reviewer
* helpful - helpfulness rating of the review, e.g. 2/3
* reviewText - text of the review
* overall - rating of the product
* summary - summary of the review
* unixReviewTime - time of the review (unix time)
* reviewTime - time of the review (raw)

 Here is the code to read the data into a pandas data frame:

```python
import pandas as pd
import gzip

def parse(path):
  g = gzip.open(path, 'rb')
  for l in g:
    yield eval(l)

def getDF(path):
  i = 0
  df = {}
  for d in parse(path):
    df[i] = d
    i += 1
  return pd.DataFrame.from_dict(df, orient='index')

df = getDF('reviews_Video_Games.json.gz')
```
