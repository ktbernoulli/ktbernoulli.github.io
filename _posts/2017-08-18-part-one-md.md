---
title: "tDM : An example of a .md of your Jupyter Notebook"
layout: post
date: 2017-08-18
tag: posting
projects: false
hidden: true # don't count this post in blog pagination
description: "A render that is clean because it does not show all the features of the original notebook."
category: instruction
author: kateta
externalLink: false
---

# Business Objective :
What are the top ten towers that are projected to have a fault severity of 2?


## Step 0 : Importing the data.
All of the data for this project are divided into five different datasets. <br>
They all have the common column of id, so we will have to merge them together for consistent cleaning.


```python
import pandas as pd
resources = pd.read_csv("data/resource_type.csv")
severity = pd.read_csv("data/severity_type.csv")
log = pd.read_csv("data/log_feature.csv")
event = pd.read_csv("data/event_type.csv")
train = pd.read_csv("data/train.csv")
```

Thinking towards the modeling part, we should really differentiate now <br> 
which are considered the independent variables vs the dependent variables right now.

## Step 1 : Merging all of the data sets together.

### Here are the independent variables.


```python
# This is called the leftside data set because it will be merged on the left at the end.
leftside = pd.merge(left = log, right = event) 
# This is called the rightside data set because it will be merged on the right at the end.
rightside = pd.merge(left = resources, right = severity)
# Here is the combined independent variables data set.
independent = pd.merge(left = leftside, right = rightside, on='id')
```

### Here is now the entire data combined.


```python
data = pd.merge(left = train, right = independent, on='id')
print data.info()
data.head()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 61839 entries, 0 to 61838
    Data columns (total 8 columns):
    id                61839 non-null int64
    location          61839 non-null object
    fault_severity    61839 non-null int64
    log_feature       61839 non-null object
    volume            61839 non-null int64
    event_type        61839 non-null object
    resource_type     61839 non-null object
    severity_type     61839 non-null object
    dtypes: int64(3), object(5)
    memory usage: 4.2+ MB
    None
    




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>location</th>
      <th>fault_severity</th>
      <th>log_feature</th>
      <th>volume</th>
      <th>event_type</th>
      <th>resource_type</th>
      <th>severity_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>14121</td>
      <td>location 118</td>
      <td>1</td>
      <td>feature 312</td>
      <td>19</td>
      <td>event_type 34</td>
      <td>resource_type 2</td>
      <td>severity_type 2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>14121</td>
      <td>location 118</td>
      <td>1</td>
      <td>feature 312</td>
      <td>19</td>
      <td>event_type 35</td>
      <td>resource_type 2</td>
      <td>severity_type 2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14121</td>
      <td>location 118</td>
      <td>1</td>
      <td>feature 232</td>
      <td>19</td>
      <td>event_type 34</td>
      <td>resource_type 2</td>
      <td>severity_type 2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>14121</td>
      <td>location 118</td>
      <td>1</td>
      <td>feature 232</td>
      <td>19</td>
      <td>event_type 35</td>
      <td>resource_type 2</td>
      <td>severity_type 2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>9320</td>
      <td>location 91</td>
      <td>0</td>
      <td>feature 315</td>
      <td>200</td>
      <td>event_type 34</td>
      <td>resource_type 2</td>
      <td>severity_type 2</td>
    </tr>
  </tbody>
</table>
</div>



## Step 2 : Clean the strings within the columns.
This is for most of the columns, the column name is within the data itself & that is redundancy. <br>
In addition, there will be some algorithms that will not be able to read strings as inputs.

### Here is a brute force way of indexing the string for just the numeric data.
There are other ways of doing this using functions within strings themselves. <br>
This code does not particularly run as fast as other methods.


```python
# Here are empty arrays for appending just the separated numericals.
location = []
logF = []
eventT = []
resourceT = []
severityT = []
# This is a for loop that runs from the beginning of the data set to the end of the data set,
# collecting only the indexes of each data point that contains the numerical part of the string.
for i in range(len(data)):
    x = data['location'][i][9:16]
    y = data['log_feature'][i][8:12]
    z = data['event_type'][i][11:14]
    a = data['resource_type'][i][14:16]
    b = data['severity_type'][i][14:16]
# Then appending it to the appropriate empty arrays to be collected.
    location.append(x)
    logF.append(y)
    eventT.append(z)
    resourceT.append(a)
    severityT.append(b)
```

### Here is the cleaned data set without additional string text.


```python
# Because it is bad habit to overwrite data, here is a new DataFrame to place information in.
cleaned = pd.DataFrame(columns = data.columns)
# This code definitely just creates an empty DataFrame with the same columns as the original data.

# id remains the same from the original.
cleaned['id'] = data['id']
# location was cleaned.
cleaned['location'] = location
# fault_severity remains the same.
cleaned['fault_severity'] = data['fault_severity']
# log_feature was cleaned.
cleaned['log_feature'] = logF
# volume remains the same.
cleaned['volume'] = data['volume']
# event_type was cleaned.
cleaned['event_type'] = eventT
# resource_type was cleaned.
cleaned['resource_type'] = resourceT
# severity_type was cleaned.
cleaned['severity_type'] = severityT

# Here is a preview of the new DataFrame.
cleaned.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>location</th>
      <th>fault_severity</th>
      <th>log_feature</th>
      <th>volume</th>
      <th>event_type</th>
      <th>resource_type</th>
      <th>severity_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>14121</td>
      <td>118</td>
      <td>1</td>
      <td>312</td>
      <td>19</td>
      <td>34</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>14121</td>
      <td>118</td>
      <td>1</td>
      <td>312</td>
      <td>19</td>
      <td>35</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14121</td>
      <td>118</td>
      <td>1</td>
      <td>232</td>
      <td>19</td>
      <td>34</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>14121</td>
      <td>118</td>
      <td>1</td>
      <td>232</td>
      <td>19</td>
      <td>35</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>9320</td>
      <td>91</td>
      <td>0</td>
      <td>315</td>
      <td>200</td>
      <td>34</td>
      <td>2</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



### Instead of constantly running this code over again,
let's export this cleaned data set to continue our analysis / project with.


```python
cleaned.to_csv("1 cleaned data.csv")
```
