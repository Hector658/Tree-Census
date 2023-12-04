# Tree-Census - Pandas Data Cleaning

## Introduction
This is my first project in data analysis, so it is very simple.
I used my skills in python and pandas in a very huge dataset of trees census in order to make it smaller and easier to read.
Specifically I managed to clean the data of dead trees or not healthy, this could be useful if we apply this for the datasets of the later years
in order to do an analysis of healthy trees population and maybe do a future analysis.


## Objectives
In this project I managed to use my skills in Python such as:
- Opening and inspecting the contents of CSVs using pandas dataframes
- Identifying duplicate columns
- Identifying and handling missing values
- Identifying and handling invalid values
- Cleaning text data by removing or replacing whitespace and fixing typos
- Plotting and analyzing graph data

## The Data
Unfortunately the file size is too big for upload it, but here is the webpage
 https://data.cityofnewyork.us/Environment/2015-Street-Tree-Census-Tree-Data/uvpi-gqnh 
it includes a CSV with the data and a PDF with very useful information.

![original_dataset](https://github.com/Hector658/Tree-Census/assets/146046209/97ab6658-283c-487c-bfbc-225e8fda77dc)




# Code
## Loading the data with pandas

```python
import pandas as pd
df = pd.read_csv(r'C:\Datos\2015_Street_Tree_Census_-_Tree_Data_20231128.csv')
df
```

## Removing the first columns
Just running the dataframe we notice that this file is really big 595592 rows × 45 columns, there is too much information available, so reading the PDF we notice that we can drop some columns
just like  'created_at', tis column contain similar dates, it does not have relation with the tree´s health, 'spc_common' is just the common name common name by which it is known, 'user_type' is 
the person who made the census for that tree in particular ', the next columns are just for the location of trees, so we are going to remove them:
'root_grate',  'address', 'postcode','zip_city', 'community board', 'borocode', 'borough', 'cncldist', 'st_assem', 'st_senate', 'nta', 'nta_name', 'boro_ct', 'state',
'latitude', 'longitude', 'x_sp', 'y_sp', 'council district','census tract', 'bin' and 'bbl', there is no information about this one in the PDF.

After this first cleaning, we create a new variable for the remaining columns

```python
data = df[['tree_id', 'block_id', 'tree_dbh', 'stump_diam',
        'status', 'health', 'spc_latin', 'steward',
       'guards', 'sidewalk', 'problems', 'root_stone',
       'root_grate', 'root_other', 'trunk_wire', 'trnk_light', 'trnk_other',
       'brch_light', 'brch_shoe', 'brch_other' ]]
data
```
And we convert this in a dataframe using the variable df again

```python
df = pd.DataFrame(data)
df
```

## Deleting non numerical values
Now we search in each column, starting for the first one.
If we take a look in the original dataframe we will realize that there are rows without a number id, actually there is no information in 
those rows, so we will delete them.
For this we will convert all the data from the first column in numerical characters, and erase every non numerical row

```python
df = df[pd.to_numeric(df['tree_id'], errors='coerce').notnull()]
df
```
Now we see if there are null values and how many are in each column

```python
df.isna().sum()
```
Doing a new analysis we realize there is a column named stump. According to the description in the attached PDF, a stump is usually a small remaining portion of the trunk with the roots still in the ground after the tree have been cut.
This still has a chance to become a tree again, so we will left all them for now
In status we don´t have any missing value.

Now we see that there are many missing values in health, but fortunately the column status could tell us the tree´s condition
We only want to keep healthy trees, so we drop every tree whose condition is not Good or Fair


## Dropping not useful rows

```python
dead_trees = df[(df['health'] != 'Fair') & (df['health'] != 'Good')].index
dead_trees
```

```python
df.drop(dead_trees , inplace=True)
df
```
An we see if there is a remaining missing value for the health column
```python
df.isna().sum()
```

There is no missing values, so let´s continue with the next column, the spc_latin
We check if we can get more information from another column, so  we show on screen this rows

```python
df[df['spc_latin'].isna()]
```
This two trees are healthy and we can´t get more information from the other columns, so we 
rename them as Unknown in order not to leave missing values.
This shouldn't be a problem because there are only two rows in this condition.

## Replacing values
```python
df['spc_latin'] = df['spc_latin'].fillna('Unknown')
```

And again just to confirm we check the missing values

```python
df.isna().sum()
```
Analyzing one more time the remaining columns we realize the stewards and sidewalk columns are not an indicator of the tree´s health
so we drop them, but first, we replace the missing values in the column guards with the caption 'No guards'

```python
df['guards'] = df['guards'].fillna('No Guards')
```

```python
df.drop(['steward', 'sidewalk'], axis=1)
```

The next columns are all about problems of the root.
This is important data, but the first column encompasses all these columns. And looking for a cleaner job
we erase all of them but the first one.

```python
df.drop(['root_stone', 'root_grate', 'root_other', 'trunk_wire', 'trnk_light',
        'trnk_other', 'brch_light', 'brch_shoe', 'brch_other'], axis=1)
```

Finally we check for the problems. If there are no values in there that means the tree has not root problems, 
so we replace the missing values with the caption 'No Problems'

```python
df['problems'] = df['problems'].fillna('No Problems')
df
```

![second](https://github.com/Hector658/Tree-Census/assets/146046209/6f4b977e-9f48-452d-bfee-1cacc27ee522)



This already looks clean, but we can change the format for problems
fortunately there is a comma separating the problems and we can replace this with a dash.

```python
df['problems'] = df['problems'].str.replace(',', '/')
```



And we have our final result.

```python
df

```




But this is a dataframe for alive trees, so the column of status seems a little redundant, we will drop it too.
## Our final Dataframe
```python
df.drop(['status'], axis=1)
```
![final_dat](https://github.com/Hector658/Tree-Census/assets/146046209/c5893784-9a12-4570-8a52-2903829aa408)



## Graphing
Finally we would like to see some information graphically represented
and we first plot the comparison between trees in good and fair state

```python
pd.DataFrame(df['health'].value_counts()).plot(kind='bar', figsize=(20,10))
```

![health_hist](https://github.com/Hector658/Tree-Census/assets/146046209/b3c1a3c7-2161-4777-8060-e5edc889cdbc)



And finally the number of trees of each specie



```python
pd.DataFrame(df['spc_latin'].value_counts()).plot(kind='bar', figsize=(20,10))
```
![spec_hist](https://github.com/Hector658/Tree-Census/assets/146046209/98040638-16a3-40dd-bb55-56264e19121c)































