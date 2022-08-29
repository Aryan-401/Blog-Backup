## Getting Started with Machine Learning

**Machine Learning**! **Artificial Intelligence**! **Computational Analysis**! All these buzz words surround the world of tech. These buzz words have created a hardened cast over the computer science community. As part of the hype, I learnt the basics of Machine Learning using `pandas` and `sklearn`. We would also use [this](https://www.kaggle.com/datasets/dansbecker/melbourne-housing-snapshot/download?datasetVersionNumber=5) dataset from Kaggle to interpret and understand our data. Enough small-talk. Let's get coding!

# Step 1: Create a Virtual Environment in Python

Data scientists usually use Jupyter Notebooks for compiling and interpreting data, so make sure you have Jupyter installed on your local machine.

Or, in case you want to work in a standard Python Environment:

Follow the steps in [this](https://aryan401.hashnode.dev/virtual-environments-youre-gonna-need-them) article, and then continue with this tutorial.

# Step 2: Download the Required Dependencies

For this initial tutorial, we will be starting with Pandas. Pandas is the primary tool data scientists use for exploring and manipulating data. Most people abbreviate pandas in their code as `pd`. We do this with the following command:

```python
import pandas as pd  #pip install pandas
```

The most important part of the Pandas library is the DataFrame. A DataFrame holds the type of data you might think of as a table. This is similar to a sheet in Excel.

Pandas has powerful methods for most things you'll want to do with this type of data and could be a mini-series on its own.

We would also be working with sklearn further, so it wouldn't hurt to install it now.

```bash
pip install scikit-learn
```
# The Basics of Pandas
Pandas is a fast, powerful, flexible, and easy-to-use open source data analysis and manipulation tool built on top of the Python programming language. It is used by millions of data scientists and is entirely open source!
## Reading a CSV file
Unzipping [this](https://www.kaggle.com/datasets/dansbecker/melbourne-housing-snapshot/download?datasetVersionNumber=5) file, we should get a file named `melb_data.csv`. Make sure it is in the same folder as your working file. and attempt to read it with the following lines of code

```python
file_path = 'melb_data.csv'
melbourne_data = pd.read_csv(file_path)
melbourne_data.describe() #should give information about independent columns in the dataset
```

## Some basic Panda's Function

To get all columns, we use `columns`
```python
print(melbourne_data.columns)
```
Output:
```python
Index(['Suburb', 'Address', 'Rooms', 'Type', 'Price', 'Method', 'SellerG',
       'Date', 'Distance', 'Postcode', 'Bedroom2', 'Bathroom', 'Car',
       'Landsize', 'BuildingArea', 'YearBuilt', 'CouncilArea', 'Lattitude',
       'Longtitude', 'Regionname', 'Propertycount'],
      dtype='object')
```

The current dataset has missing values (some houses for which some variables weren't recorded). We will learn to handle missing values in a later tutorial. So we will take the simplest option for now and drop houses from our data. Don't worry about this much for now, though the code is:

 ```python
melbourne_data = melbourne_data.dropna(axis=0)
```

Most datasets have thousands of rows and tens of columns. It is not logical to load up the entire dataFrame to have a glimpse of the data it contains. we use `head(x)` and `tail(x)` functions to get the first `x` records from the dataFrame. By default, they return five results each
```python
melbourne_data.head()  # First 5 Rows
melbourne_data.tail()  # Last 5 Rows
```


## Choosing "Features"

Columns inputted into our model are called "features" In our case, those would be the columns used to determine the home price.

```python
melbourne_features = ['Rooms', 'Bathroom', 'Landsize', 'Car', 'Postcode']
``` 
By convention, this variable is called `X`.
```python
X = melbourne_data[melbourne_features]
```
There are four major steps to making and using a model: 
- Defining what kind of model you are going to be using
- Fitting your data into the model
- Predicting data using your model
- Determining how accurate the model is using error analysis techniques

## Writing our First Model

```python
from sklearn.tree import DecisionTreeRegressor

melbourne_model = DecisionTreeRegressor(random_state=1)  # Defining what type of model we are going to be using
```

`random_state` is used to ensure the same results can be found on each run since most models allow randomness in their training. The number does not meaningfully change the results of the model.

```python
melbourne_model.fit(X, y)  # Fitting your data into the model
```

Now to predict some data, we can use the `.predict()` attribute

```python
print(melbourne_model.predict(X.head()))
print(X.head)
```

You'd notice that the price of both of these are the same, as the model is trained directly on the same values. This is called in-sampling since, in a large enough market, the door color is unrelated to the retail value of the property. However, in the model we generated, green-colored doors are, on average, much more expensive than any other colored door; thus, if we had two similar houses with different colored doors, the green-colored door would net a greater price though the detail is hardly relevant. 

To combat this, we would split our data into two parts — Training data and Validation data to calculate a mean absolute error. 

```python
from sklearn.model_selection import train_test_split

train_X, val_X, train_y, val_y = train_test_split(X, y, random_state = 0)

melbourne_model = DecisionTreeRegressor()  # Defining our model

melbourne_model.fit(train_X, train_y)  # Fitting our model

val_predictions = melbourne_model.predict(val_X)  # predictions!
print(mean_absolute_error(val_y, val_predictions))  # The greater the mean_absolute_error, the worse the model performance is.
```

## And there you go

You've created your first data model. Try experimenting with the data and finding a method to reduce the `mean_absolute_error`. The Lower the `mean_absolute_error`, the better the data model. 






