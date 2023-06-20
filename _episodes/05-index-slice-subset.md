---
title: Indexing, Slicing and Subsetting DataFrames in Python
teaching: 30
exercises: 30
questions:
    - "How can I access specific data within my data set?"
    - "How can Python and Pandas help me to analyse my data?"
objectives:
    - "Describe what 0-based indexing is."
    - "Manipulate and extract data using column headings and index locations."
    - "Employ slicing to select sets of data from a DataFrame."
    - "Employ label and integer-based indexing to select ranges of data in a dataframe."
    - "Reassign values within subsets of a DataFrame."
    - "Create a copy of a DataFrame."
    - "Query / select a subset of data using a set of criteria using the following operators:
       `==`, `!=`, `>`, `<`, `>=`, `<=`."
    - "Locate subsets of data using masks."
    - "Describe BOOLEAN objects in Python and manipulate data using BOOLEANs."
keypoints:
    - "In Python, portions of data can be accessed using indices, slices, column headings, and
       condition-based subsetting."
    - "Python uses 0-based indexing, in which the first element in a list, tuple or any other data
       structure has an index of 0."
    - "Pandas enables common data exploration steps such as data indexing, slicing and conditional
       subsetting."
---

In the first episode of this lesson, we read a CSV file into a pandas' DataFrame. We learned how to:

- save a DataFrame to a named object,
- perform basic math on data,
- calculate summary statistics, and
- create plots based on the data we loaded into pandas.

In this lesson, we will explore ways to access different parts of the data using:

- indexing,
- slicing, and
- subsetting.

## Loading our data

We will continue to use the waves dataset that we worked with in the last
episode. If you need to, reopen and read in the data again:

~~~
# Make sure pandas is loaded
import pandas as pd

# Read in the wave CSV
waves_df = pd.read_csv("data/waves.csv")
~~~
{: .language-python}

## Indexing and Slicing in Python

We often want to work with subsets of a **DataFrame** object. There are
different ways to accomplish this including: using labels (column headings),
numeric ranges, or specific x,y index locations.


## Selecting data using Labels (Column Headings)

We use square brackets `[]` to select a subset of a Python object - this is the
same whether it's a list, a NumPy ndarray, or a Pandas DataFrame. For example,
we can select all data from a column named `buoy_id` from the `waves_df`
DataFrame by name. There are two ways to do this:

~~~
# TIP: use the .head() method we saw earlier to make output shorter
# Method 1: select a 'subset' of the data using the column name
waves_df['buoy_id']

# Method 2: with Pandas, we can also use the column name as an 'attribute' if
# it's a single word, and this gives the same output
waves_df.buoy_id

# These also give the same output:
waves_df['buoy_id'].head()
waves_df.buoy_id.head()
~~~
{: .language-python}

We can also create a new object that contains only the data within the
`buoy_id` column as follows:

~~~
# Creates an object, waves_buoy, that only contains the `buoy_id` column
waves_buoy = waves_df['buoy_id']
~~~
{: .language-python}

We can pass a list of column names too, as an index to select columns in that
order. This is useful when we need to reorganize our data.

**NOTE:** If a column name is not contained in the DataFrame, an exception
(error) will be raised.

~~~
# Select the buoy and plot columns from the DataFrame
waves_df[['buoy_id', 'record_id']]

# What happens when you flip the order?
waves_df[['record_id', 'buoy_id']]

# What happens if you ask for a column that doesn't exist?
waves_df['Bbuoys']
~~~
{: .language-python}

Python tells us what type of error it is in the traceback, at the bottom it says
`KeyError: 'Bbuoys'` which means that `Bbuoys` is not a valid column name (nor a valid key in
the related Python data type dictionary).

> ## Reminder
> The Python language and its modules (such as Pandas) define reserved
> words that should not be used as identifiers when assigning objects
> and variable names. Examples of reserved words in Python include Boolean values
> `True` and `False`, operators `and`, `or`, and `not`, among others. The full list
> of reserved words for Python version 3 is provided at
> <https://docs.python.org/3/reference/lexical_analysis.html#identifiers>.
>
> When naming objects and variables, it's also important to avoid using
> the names of built-in data structures and methods. For example, a _list_ is a built-in
> data type. It is possible to use the word 'list' as an identifier for a new object,
> for example `list = ['apples', 'oranges', 'bananas']`. However, you would then
> be unable to create an empty list using `list()` or convert a tuple to a
> list using `list(sometuple)`.
{: .callout}

## Extracting Range based Subsets: Slicing

> ## Reminder
> Python uses 0-based indexing.
{: .callout}

Let's remind ourselves that Python uses 0-based
indexing. This means that the first element in an object is located at position
`0`. This is different from other tools like R and Matlab that index elements
within objects starting at 1.

~~~
# Create a list of numbers:
a = [1, 2, 3, 4, 5]
~~~
{: .language-python}

![indexing diagram](../fig/slicing-indexing.png)
![slicing diagram](../fig/slicing-slicing.png)


> ## Challenge - Extracting data
>
> 1. What value does the code below return?
>
>    ~~~
>    a[0]
>    ~~~
>    {: .language-python }
>
> 2. How about this:
>
>    ~~~
>    a[5]
>    ~~~
>    {: .language-python }
>
> 3. In the example above, calling `a[5]` returns an error. Why is that?
>
> 4. What about?
>
>    ~~~
>    a[len(a)]
>    ~~~
>    {: .language-python }
{: .challenge}


## Slicing Subsets of Rows in Python

Slicing using the `[]` operator selects a set of rows and/or columns from a
DataFrame. To slice out a set of rows, you use the following syntax:
`data[start:stop]`. When slicing in pandas the start bound is included in the
output. The stop bound is one step BEYOND the row you want to select. So if you
want to select rows 0, 1 and 2 your code would look like this:

~~~
# Select rows 0, 1, 2 (row 3 is not selected)
waves_df[0:3]
~~~
{: .language-python}

The stop bound in Python is different from what you might be used to in
languages like Matlab and R.

~~~
# Select the first 5 rows (rows 0, 1, 2, 3, 4)
waves_df[:5]

# Select the last element in the list
# (the slice starts at the last element, and ends at the end of the list)
waves_df[-1:]
~~~
{: .language-python}

Pandas also recognises the _step_ parameter:

~~~
# return every other row in the first ten rows
waves_df[0:10:2]
~~~
{: .language-python}

We can also reassign values within subsets of our DataFrame.

But before we do that, let's look at the difference between the concept of
copying objects and the concept of referencing objects in Python.

## Copying Objects vs Referencing Objects in Python

Let's start with an example:

~~~
# Using the 'copy() method'
true_copy_waves_df = waves_df.copy()

# Using the '=' operator
ref_waves_df = waves_df
~~~
{: .language-python}

You might think that the code `ref_waves_df = waves_df` creates a fresh
distinct copy of the `waves_df` DataFrame object. However, using the `=`
operator in the simple statement `y = x` does **not** create a copy of our
DataFrame. Instead, `y = x` creates a new variable `y` that references the
**same** object that `x` refers to. To state this another way, there is only
**one** object (the DataFrame), and both `x` and `y` refer to it.

In contrast, the `copy()` method for a DataFrame creates a true copy of the
DataFrame.

Let's look at what happens when we reassign the values within a subset of the
DataFrame that references another DataFrame object:

~~~
# Assign the value `0` to the first three rows of data in the DataFrame
ref_waves_df[0:3] = 0
~~~
{: .language-python}

Let's try the following code:

~~~
# ref_waves_df was created using the '=' operator
ref_waves_df.head()

# true_copy_waves_df was created using the copy() function
true_copy_waves_df.head()

# waves_df is the original dataframe
waves_df.head()
~~~
{: .language-python}

What is the difference between these three dataframes?

When we assigned the first 3 rows the value of `0` using the
`ref_waves_df` DataFrame, the `waves_df` DataFrame is modified too.
Remember we created the reference `ref_waves_df` object above when we did
`ref_waves_df = waves_df`. Remember `waves_df` and `ref_waves_df`
refer to the same exact DataFrame object. If either one changes the object,
the other will see the same changes to the reference object.

However - `true_copy_waves_df` was created via the `copy()` function.
It retains the original values for the first three rows.

**To review and recap**:

- **Copy** uses the dataframe's `copy()` method

  ~~~
  true_copy_waves_df = waves_df.copy()
  ~~~
  {: .language-python }
- A **Reference** is created using the `=` operator

  ~~~
  ref_waves_df = waves_df
  ~~~
  {: .language-python }

## Inserting columns

You can insert a column of data by specifying a column name that doesn't already exist
and passing a list of the same length as the number of rows; e.g.

waves_df["new_column"] = range(0,2073)


Okay, that's enough of that. Let's create a brand new clean dataframe from
the original data CSV file.

~~~
waves_df = pd.read_csv("data/waves.csv")
~~~
{: .language-python}

## Slicing Subsets of Rows and Columns in Python

We can select specific ranges of our data in both the row and column directions
using either label or integer-based indexing.

- `loc` is primarily *label* based indexing. *Integers* may be used but
  they are interpreted as a *label*.
- `iloc` is primarily *integer* based indexing

To select a subset of rows **and** columns from our DataFrame, we can use the
`iloc` method. For example, for the first 3 rows, we can select record_id, name, and date (columns 0, 2,
and 3 when we start counting at 0), like this:

~~~
# iloc[row slicing, column slicing]
waves_df.iloc[0:3, [0,2,3]]
~~~
{: .language-python}

which gives the **output**

~~~

  record_id                             Name                Date
0         1  SW Isles of Scilly WaveNet Site    17/04/2023 00:00
1         2         Hayling Island Waverider    17/04/2023 00:00
2         3      Firth of Forth WaveNet Site    17/04/2023 00:00
~~~
{: .output}

Notice that we asked for a slice from 0:3. This yielded 3 rows of data. When you
ask for 0:3, you are actually telling Python to start at index 0 and select rows
0, 1, 2 **up to but not including 3**.

Let's explore some other ways to index and select subsets of data:

~~~
# Select all columns for rows of index values 0 and 10
waves_df.loc[[0, 10], :]

# What does this do?
waves_df.loc[0, ['buoy_id', 'record_id', 'Wave Height']]

# What happens when you type the code below?
waves_df.loc[[0, 10, 35549], :]
~~~
{: .language-python}

**NOTE 1**: with our dataset, we are using integers even when using `loc` because our DataFrame index
(which is the unnamed first column) is composed of integers - but Pandas converts these to strings

**NOTE 2**: Labels must be found in the DataFrame or you will get a `KeyError`.

Indexing by labels `loc` differs from indexing by integers `iloc`.
With `loc`, both the start bound and the stop bound are **inclusive**. When using
`loc`, integers *can* be used, but the integers refer to the
index label and not the position. For example, using `loc` and select 1:4
will get a different result than using `iloc` to select rows 1:4.

We can also select a specific data value using a row and
column location within the DataFrame and `iloc` indexing:

~~~
# Syntax for iloc indexing to finding a specific data element
dat.iloc[row, column]
~~~
{: .language-python}

In this `iloc` example,

~~~
waves_df.iloc[2, 6]
~~~
{: .language-python}

gives the **output**

~~~
4.5
~~~
{: .output}

Remember that Python indexing begins at 0. So, the index location [2, 6]
selects the element that is 3 rows down and 7 columns over (Tpeak) in the DataFrame.

It is worth noting that rows are selected when using `loc` with a single list of
labels (or `iloc` with a single list of integers). However, unlike `loc` or `iloc`,
indexing a data frame directly with labels will select columns (e.g. 
`waves_df[['buoy_id', 'Name', 'Temperature']]`), while ranges of integers will
select rows (e.g. waves_df[0:13]) - but passing a single integer will raise an error.
Direct indexing of rows is redundant with using `iloc`, and will raise a `KeyError` if a single integer or list is used:

~~~
# produces an error - even though you might think it looks sensible
waves_df.loc[1:10,1]
~~~
{: .language-python}


the error will also occur if index labels are used without `loc` (or column labels used
with it).
A useful rule of thumb is the following: 
 - integer-based slicing of rows is best done with `iloc` and will avoid errors - it is generally consistent with indexing of Numpy
arrays)
 - label-based slicing of rows is done with `loc`
 - slicing of columns by directly indexing column names.


> ## Challenge - Range
>
> 1. What happens when you execute:
>
>    - `waves_df[0:1]`
>    - `waves_df[0]`
>    - `waves_df[:4]`
>    - `waves_df[:-1]`
>
> 2. What happens when you call:
>
>    - `waves_df.iloc[0:1]`
>    - `waves_df.iloc[0]`
>    - `waves_df.iloc[:4, :]`
>    - `waves_df.iloc[0:4, 1:4]`
>    - `waves_df.loc[0:4, 1:4]`
>
> - How are the last two commands different?
{: .challenge}


## Subsetting Data using Criteria

We can also select a subset of our data using criteria. For example, we can
select all rows that have a temperature less than or equal to 10 degrees

~~~
waves_df[waves_df.Temperature <= 10]
~~~



Which produces the following output:

~~~

    record_id    buoy_id                Name                Date     Tz    Peak Direction    Tpeak    Wave Height    Temperature    Spread    Operations    Seastate    Quadrant
3           4	         3    Chesil Waverider    17/04/2023 00:00    5.5             225.0      8.3           0.50          10.20      48.0          crew       swell       south
10         11          3    Chesil Waverider    15/04/2023 00:00    3.2             260.0      3.4           0.21           8.95      67.0          crew     windsea        west
~~~
{: .language-python}

Or, we can select all rows that have a buoy_id of 3:

~~~
waves_df[waves_df.buoy_id == 3]
~~~
{: .language-python}


We can also select all rows that do not contain values for Tpeak (listed as NaN):

~~~
waves_df[waves_df["Tpeak"].isna()]
~~~
{: .language-python}

Or we can select all rows that do not contain the buoy_id 3:

~~~
waves_df[waves_df.buoy_id != 3]
~~~
{: .language-python}

We can define sets of criteria too, for example selecting only waves
with a height between 3.0 and 4.0 metres:

~~~
waves_df[(waves_df["Wave Height"] >= 3.0) & (waves_df["Wave Height"] < 4.0)]
~~~
{: .language-python}

## Different types of and
> In Python, we can normally use the keyword `and` for the Boolean _and_ operator:
> ~~~
> x = True
> y = True
> x and y
> ~~~
> {: .language-python}
>
> ~~~
> True
> ~~~
> {: .output}
>
> ~~~
> x = True
> y = False
> x and y
> ~~~
> {: .language-python}
>
> ~~~
> False
> ~~~
> {: .output}
>
> _But_, in Pandas we need to use the `&` symbol instead. The `and` operator requires "Truth-values", but `Series` of Booleans
> is considered "ambiguous", and trying to use `and` returns an error `Truth value of a Series is ambiguous.`
{: .callout}  


### Python Syntax Cheat Sheet

We can use the syntax below when querying data by criteria from a DataFrame.
Experiment with selecting various subsets of the "waves" data.

* Equals: `==`
* Not equals: `!=`
* Greater than, less than: `>` or `<`
* Greater than or equal to `>=`
* Less than or equal to `<=`


> ## Challenge - Queries
>
> 1. Select a subset of rows in the `waves_df` DataFrame that contain data from
>   the year 2022 and that contain Temperature values less than or equal to 8. How
>   many rows did you end up with? You may want to create a new column containing the dates
>   formatted as DateType that we created earlier
>
> 2. You can use the `isin` command in Python to query a DataFrame based upon a
>   list of values as follows:
>
>    ~~~
>    waves_df[waves_df['buoy_id'].isin([listGoesHere])]
>    ~~~
>    {: .language-python }
>
>   Use the `isin` function to find all plots that contain buoy ids 5 and 7
>   in the "waves" DataFrame. How many records contain these values?
>
> 3. Experiment with other queries. Create a query that finds all rows with a
>   Tpeak greater than or equal to 10.
>
> 4. The `~` symbol in Python can be used to return the OPPOSITE of the
>   selection that you specify in Python. It is equivalent to **is not in**.
>   Write a query that selects all rows with Quadrant NOT equal to 'south' or 'east' in
>   the "waves" data.
{: .challenge}


# Using masks to identify a specific condition

A **mask** can be useful to locate where a particular subset of values exist or
don't exist - for example,  NaN, or "Not a Number" values. To understand masks,
we also need to understand `BOOLEAN` objects in Python.

Boolean values include `True` or `False`. For example,

~~~
# Set x to 5
x = 5

# What does the code below return?
x > 5

# How about this?
x == 5
~~~
{: .language-python}

When we ask Python whether `x` is greater than 5, it returns `False`.
This is Python's way to say "No". Indeed, the value of `x` is 5,
and 5 is not greater than 5.

To create a boolean mask:

- Set the True / False criteria (e.g. `values > 5 = True`)
- Python will then assess each value in the object to determine whether the
  value meets the criteria (True) or not (False).
- Python creates an output object that is the same shape as the original
  object, but with a `True` or `False` value for each index location.

Let's try this out. Let's identify all locations in the wave data that have
null (missing or NaN) data values. We can use the `isnull` method to do this.
The `isnull` method will compare each cell with a null value. If an element
has a null value, it will be assigned a value of  `True` in the output object.

~~~
pd.isnull(waves_df)
~~~
{: .language-python}

A snippet of the output is below:

~~~
    record_id    buoy_id     Name     Date       Tz    Peak Direction    Tpeak    Wave Height    Temperature    Spread    Operations    Seastate    Quadrant
0       False      False    False    False    False             False    False          False          False     False         False       False       False
1       False      False    False    False    False             False    False          False          False     False         False       False       False
2       False      False    False    False    False             False    False          False          False     False         False       False       False
~~~
{: .language-python}

To select the rows where there are null values, we can use
the mask as an index to subset our data as follows:

~~~
# To select just the rows with NaN values, we can use the 'any()' method
waves_df[pd.isnull(waves_df).any(axis=1)]
~~~
{: .language-python}

Note that the `Temperature` and other columns of our DataFrame contains many `null` or `NaN`
values. Remember we've disucssed ways of dealing with this in the previous episode on [Data Types and Formats]({{ page.root }}{% link _episodes/04-data-types-and-format.md %}).

As we saw earlier, we can run `isnull` on a particular column too. What does the code below do?

~~~
# What does this do?
empty_Temperatures = waves_df[pd.isnull(waves_df['Temperature'])]['Temperature']
print(empty_Temperatures)
~~~
{: .language-python}

Let's take a minute to look at the statement above. We are using the Boolean
object `pd.isnull(waves_df['Temperature'])` as an index to `waves_df`. We are
asking Python to select rows that have a `NaN` value of Temperature.


> ## Challenge - Putting it all together
>
> 1. Create a new DataFrame that only contains observations with Operations values that
>   are **not** crew or survey. Print the number of rows in this new DataFrame.
>   Verify the result by comparing the number of rows in the new DataFrame with
>   the number of rows in the waves DataFrame where Site Type is No Go.
>
> 2. Create a new DataFrame that contains only observations from the Chesil Waverider of 
>  Hayling Island Waverider buoys, and where the wave height is less than 50 cm. 
>
> 3. Create a new DataFrame that contains only observations that are of Quadrant
>  north or west and where Tpeak values are greater than 10. 
>
>> ## Solution
>> ~~~
>> waves_df[(waves_df["buoy_id"].isin([3,7])) & (waves_df["Wave Height"] < 0.5)]
>> ~~~
>> {: .language-python}
> {: .solution}
{: .challenge}

{% include links.md %}
