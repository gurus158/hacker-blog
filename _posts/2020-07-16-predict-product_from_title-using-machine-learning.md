# Predicting Product from title using machine learning(AI) model

Imagine you get  thousands of product titles from some E-commerce websites and your task is to assign  value in "Product" column based on titles. 

For example:  Title "extreme sale 141gr aveeno baby eczema therapy moisturizing cream"  is assigned product equals to "baby cream" .

After full day hard-work you finally  assigned product to all titles. But you have to do it daily . Some how you manged to do it for a week but now you start asking can it be done by using computer programs? Off course it can be done using computers, question should be, how?

There can be many solutions. I already have used symbolic AI i.e rule based , where we try to enumerate data based on rules derived from data we have already assigned. Rules can be like "if title contains 'cream' then product is 'cream' " something like that. Only issue with this is we have to hard code rules and rules need to be updated frequently which can be a head ache. However if task is simple and can be solved using Symbolic A.I one should prefer this.

But here I want to discuss other approach i.e Machine learning , creating neural network , etc. etc. 

I will be using python as main programming language and sklearn, pandas, matplot module for implementing ML.

lets understand what we want from algo.

We have a title , an English senetence, and we have to classify it into a product so...

Input: title_en

Output: product

This type of problem can be  solved by **Supervise Machine Learning**  in which we have training data which already have been categorized  correctly and using this data we build a model which  is able to categorized data which it hasn't seen yet.

I have the sample data in csv format which has title and product assigned. let first import this file in a dataframe.

```python
import pandas as pd
df=pd.read_csv('mlTest.csv')
df=df[['Product','title_en']] #as we only need these two columns 
```

also we need to categorize products into integer values as integers works better than text for categorizing data.

```python
df['category_id']=df['Product'].factorize()[0]
category_id_df = df[['Product', 'category_id']].drop_duplicates().sort_values('category_id')
category_to_id = dict(category_id_df.values)
id_to_category = dict(category_id_df[['category_id', 'Product']].values)
```

I basically assigned an integer to each different product calling it **category_id** ,  also created two dictionaries category_id_df and category_to_id to quickly get value of category_id based on product and vice versa.

lets plot the data to visualize it better ......

```python
%matplotlib inline
import matplotlib.pyplot as plt
df.head()
fig = plt.figure(figsize=(8,6))
df.groupby('Product').title_en.count().plot.bar(ylim=0)
plt.show()
```

![7.png](https://github.com/gurus158/blogs/blob/gh-pages/images/7.png?raw=true)

This is product vs count plot. Baby liquid Soap is major data then baby shampoo and so on.

As we want to categorize title to different product, we have to visualize data more technically , basically i want to find what are most occurring unigrams and bigrams in title based on different products. For that i'm gonna use **term frequency–inverse document frequency** in short **tfidf** and vectorize data so that Our Ml can extract some more useful information and perform better.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.feature_extraction.text import TfidfTransformer
import numpy as np
from nltk.corpus import stopwords
stop_words = set(stopwords.words('english'))
tfidf = TfidfVectorizer(sublinear_tf=True, min_df=5, norm='l2', encoding='UTF-8', ngram_range=(1, 2), stop_words=stop_words)
features = tfidf.fit_transform(df.title_en).toarray()
labels = df.category_id
print(features.shape) # printing number of features by which all data can be represent
N = 2  # as we are looking best two unigrams and bigrams
for Product, category_id in sorted(category_to_id.items()):
  features_chi2 = chi2(features, labels == category_id)
  indices = np.argsort(features_chi2[0])
  feature_names = np.array(tfidf.get_feature_names())[indices]
  unigrams = [v for v in feature_names if len(v.split(' ')) == 1]
  bigrams = [v for v in feature_names if len(v.split(' ')) == 2]
  print("# '{}':".format(Product))
  print("  . Most correlated unigrams:\n. {}".format('\n. '.join(unigrams[-N:])))
  print("  . Most correlated bigrams:\n. {}".format('\n. '.join(bigrams[-N:])))
```

Output of above is...

```
# 'Baby Conditioner':
  . Most correlated unigrams:
. house
. shinydrop
  . Most correlated bigrams:
. milk house
. kids shinydrop
# 'Baby Cream':
  . Most correlated unigrams:
. eczema
. cream
  . Most correlated bigrams:
. relief moisturizing
. moisturizing cream
# 'Baby Liquid Soap':
  . Most correlated unigrams:
. ultra
. bath
  . Most correlated bigrams:
. bath wash
. baby bath
# 'Baby Lotion':
  . Most correlated unigrams:
. daily
. lotion
  . Most correlated bigrams:
. baby daily
. baby lotion
# 'Baby Rash Cream':
  . Most correlated unigrams:
. diaper
. rash
  . Most correlated bigrams:
. diaper rash
. rash cream
# 'Baby Shampoo':
  . Most correlated unigrams:
. gentle
. shampoo
  . Most correlated bigrams:
. baby gentle
. wash shampoo
# 'Baby Soap':
  . Most correlated unigrams:
. bar
. treatment
  . Most correlated bigrams:
. therapy soothing
. bath treatment
# 'Multi Products':
  . Most correlated unigrams:
. set
. gift
  . Most correlated bigrams:
. gift set
. baby gift
# 'Other Products':
  . Most correlated unigrams:
. cleanser
. kids
  . Most correlated bigrams:
. johnsons active
. active kids
# 'baby shampoo':
  . Most correlated unigrams:
. cetaphil
. 200ml
  . Most correlated bigrams:
. baby shampoo
. shampoo 200ml
```

now data make more sense. And from here we can start create out network model or ML. As i am testing out what model should fit best.. i will implement 4 models ( **LinearSVC** , **LogisticRegression** , **MultinomialNB** , **RandomForestClassifier** ) and compare their accuracy. All four have their own theory which i'm not discuss here (i'm still understanding them) but we will use sklearn which help us to implement it without worrying about whats going under the hood.

before that we also going to split our input dataframe in two : train dataset and test dataset in apx 70:30 ratio. We will use train dataset for training and test dataset as test out model.

```python
X_train, X_test, y_train, y_test = train_test_split(df['title_en'], df['Product'], random_state = 0)
count_vect = CountVectorizer()
X_train_counts = count_vect.fit_transform(X_train)
tfidf_transformer = TfidfTransformer()
X_train_tfidf = tfidf_transformer.fit_transform(X_train_counts)
```

now we define our models and feed them with training dataset

```python
# defining all four models
models = [
    RandomForestClassifier(n_estimators=200, max_depth=3, random_state=0),
    LinearSVC(),
    MultinomialNB(),
    LogisticRegression(random_state=0),
]
CV = 10 # cross validator high means more bias , low means less bias 
cv_df = pd.DataFrame(index=range(CV * len(models)))
entries = []
for model in models: # run each model and find its cv-score
  model_name = model.__class__.__name__
  accuracies = cross_val_score(model, features, labels, scoring='accuracy', cv=CV) 
  for fold_idx, accuracy in enumerate(accuracies):
    entries.append((model_name, fold_idx, accuracy))
cv_df = pd.DataFrame(entries, columns=['model_name', 'fold_idx', 'accuracy']) # final dataframe containg model and acuuracy
cv_df.groupby('model_name').accuracy.mean() 
```

output of above is (it will take some time depends on cpu and ram of the pc)

```
model_name
LinearSVC                 0.986041
LogisticRegression        0.980040
MultinomialNB             0.964373
RandomForestClassifier    0.779087
Name: accuracy, dtype: float64
```

as you can see  

[LinearSVC]: https://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html	"LinearSVC"

has performed  best here with 98.6% accuracy

so lets check this one out on some random samples from different file which i did not use while training and testing. 

Sample data in file (categorized by a human)

| title                                                        | Product          |
| ------------------------------------------------------------ | ---------------- |
| johnsons baby shampoo 50ml                                   | Baby Shampoo     |
| JOHNSONS BABY BATH MILK AND RICE 400ML EXP2021               | Baby Liquid Soap |
| cheapest aveeno baby soothing relief moisturizing cream 226gr. 8oz | Baby Cream       |
| wholesale - aveeno baby soothing relief moisturizing cream 141gr baby lotion | Baby Lotion      |

to test model we will use following code

```python
clf = LinearSVC().fit(X_train_tfidf, y_train) 
print(clf.predict(count_vect.transform(["johnsons baby shampoo 50ml"]))) 
print(clf.predict(count_vect.transform(["JOHNSONS BABY BATH MILK AND RICE 400ML EXP2021"])))
print(clf.predict(count_vect.transform(["cheapest aveeno baby soothing relief moisturizing cream 226gr. 8oz"])))
print(clf.predict(count_vect.transform(["wholesale - aveeno baby soothing relief moisturizing cream 141gr baby lotion"])))
```

output of above is

```
['Baby Shampoo']
['Baby Liquid Soap']
['Baby Cream']
['Baby Lotion']
```

which is correct and you can see how smartly it has predicted **baby lotion**  although its title is very similar to **baby cream** ( I'm impressed ) 

That's it !! 

good bye, Happy coding!!