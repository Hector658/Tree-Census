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

## The Data
Unfortunately the file size is too big for upload it, but here is the webpage
 https://data.cityofnewyork.us/Environment/2015-Street-Tree-Census-Tree-Data/uvpi-gqnh 
it includes a CSV with the data and a PDF with very useful information.

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
       'brch_light', 'brch_shoe', 'brch_other', ]]
data
```
And we convert this in a dataframe using the variable df again

```python
df = pd.DataFrame(data)
df
```
Now we search in each column, starting for the first one.
If we take a look in the original dataframe we will realize that there are rows without a number id, actually there is no information in 
those rows, so we will delete them.
For this we will convert all the data from the first column in numerical characters, and erase every non numerical row

```python
df = df[pd.to_numeric(df['tree_id'], errors='coerce').notnull()]
df
```
Now let´s see if there are null values and how many are in each column

```python
df.isna().sum()
```





