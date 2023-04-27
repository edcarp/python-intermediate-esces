---
title: Starting With Data
teaching: 30
exercises: 30
questions:
    - "How can I import data in Python?"
    - "What is Pandas?"
    - "Why should I use Pandas to work with data?"
objectives:
    - "Navigate the workshop directory and download a dataset."
    - "Explain what a library is and what libraries are used for."
    - "Describe what the Python Data Analysis Library (Pandas) is."
    - "Load the Python Data Analysis Library (Pandas)."
    - "Read tabular data into Python using Pandas."
    - "Describe what a DataFrame is in Python."
    - "Access and summarize data stored in a DataFrame."
    - "Define indexing as it relates to data structures."
    - "Perform basic mathematical operations and summary statistics on data in a Pandas DataFrame."
    - "Create simple plots."
keypoints:
    - "Libraries enable us to extend the functionality of Python."
    - "Pandas is a popular library for working with data."
    - "A Dataframe is a Pandas data structure that allows one to access data by column (name or index) or row."
    - "Aggregating data using the `groupby()` function enables you to generate useful summaries of data quickly."
    - "Plots can be created from DataFrames or subsets of data that have been generated with `groupby()`."
---

# Working With Pandas DataFrames in Python

We can automate the process of performing data manipulations in Python. It's efficient to spend time
building the code to perform these tasks because once it's built, we can use it
over and over on different datasets that use a similar format. This makes our
methods easily reproducible. We can also easily share our code with colleagues
and they can replicate the same analysis.

### Starting in the same spot

To help the lesson run smoothly, let's ensure everyone is in the same directory.
This should help us avoid path and file name issues. At this time please
navigate to the workshop directory. If you are working in Jupyter Notebook be sure
that you start your notebook in the workshop directory.

A quick aside that there are Python libraries like [OS Library][os-lib] that can work with our
directory structure, however, that is not our focus today.

### Our Data


For this lesson we will be using a subset of data from Centre for Environment Fisheries and Aquaculture Science (Cefas). 
WaveNet, Cefas’ strategic wave monitoring network for the United Kingdom, provides a single source of real-time wave data from a network of wave buoys located in areas at risk from flooding.
https://wavenet.cefas.co.uk/

We are studying the wave height and temperature in the seas around the UK. 
The dataset is stored as a `.csv` file: each row holds information for a
single wave buoy, and the columns represent:

| Column           | Description                        |
|------------------|------------------------------------|
|record_id         | Unique id for the observation      |
|buoy_id           | Unique id for the wave buoy        |
|Name              | Name of buoy                       |
|latitude          | latitude of buoy  (degrees)        |
|longitude         | longitude of buoy (degrees)        |
|Country           | Country operating the wave buoy    |
| Date             | Date in day/month/year             |
|Tz	               | The average wave period            |
|Peak Direction	   | The direction at Tpeak             |
|Tpeak	           | Dominant wave period               |
|Wave Height	     | Significan wave height in metres   |
|Temperature       | Water temperature in degrees C     |
|Spread	           | The directional spread at Tpeak    |
|------------------|------------------------------------|


If we look out to sea, we notice that waves on the sea surface are not simple sinusoids. The surface appears to be composed of random waves of various lengths and periods. How can we describe this complex surface? 

By making some simplifications and assumptions, we fit an idealised 'spectrum' to describe all the energy held in different wave frequencies. This contains information about the wave energy at a point, covering the energy in small ripples (high frequency) to long period (low frequency) swell waves. This figure shows an example idealised JONSWAP spectrum, with the highest energy around wave periods of 11 seconds.

![WaveSpectra](https://user-images.githubusercontent.com/17011250/234795170-53ebf629-6603-4296-8c0c-55bef733071c.png)

We can go a step further, and also associate a wave direction with the amount of energy. These simplifications lead to a 2D wave spectrum at any point in the sea, with dimensions frequency and direction. Directional spreading is a measure of how wave energy for a given sea state is spread as a function of direction of propagation. For example the wave data on the left have a small directional spread, as the waves travel, this can fan out over a wider range of directions.

![Spreading](https://user-images.githubusercontent.com/17011250/234794959-8327f4b0-3e1d-447f-b467-661295d5c707.png)

The first few rows of our first file look like this:
~~~
record_id	buoy_id	Name	Country	Site Type	latitude	longitude	Date	Tz	Peak Direction	Tpeak	Wave Height	Temperature	Spread
1	14	SW Isles of Scilly WaveNet Site	England	Ocean	49.82	-6.54	17/04/2023	7.2	263	10	1.8	10.8	26
2	7	Hayling Island Waverider	England	Coastal	50.73	-0.96	17/04/2023	4	193	11.1	0.2	10.2	14
3	5	Firth of Forth WaveNet Site	Scotland	Ocean	56.19	-2.5	17/04/2023	3.7	115	4.5	0.6	7.8	28
4	3	Chesil Waverider	England	Coastal	50.6	-2.52	17/04/2023	5.5	225	8.3	0.5	10.2	48
5	10	M6 Buoy	Ireland	Ocean	53.06	-15.93	17/04/2023	7.6	240	11.7	4.5	11.5	89
6	9	Lomond	Scotland	Ocean	57.2	2.2	17/04/2023	4	NaN	NaN	0.5	NaN	NaN
7	2	Cardigan Bay	Wales	Coastal	52.433333	-4.8	17/04/2023	5.9	239	10.5	0.69	9.9	18
~~~
{: .output}

---   

## About Libraries
A library in Python contains a set of tools (called functions) that perform
tasks on our data. Importing a library is like getting a piece of lab equipment
out of a storage locker and setting it up on the bench for use in a project.
Once a library is set up, it can be used or called to perform the task(s)
it was built to do.

## Pandas in Python
One of the best options for working with tabular data in Python is to use the
[Python Data Analysis Library][pandas] (a.k.a. Pandas). The
Pandas library provides data structures, produces high quality plots with
[matplotlib][matplotlib] and integrates nicely with other libraries
that use [NumPy][numpy] (which is another Python library) arrays.

Python doesn't load all of the libraries available to it by default. We have to
add an `import` statement to our code in order to use library functions. To import
a library, we use the syntax `import libraryName`. If we want to give the
library a nickname to shorten the command, we can add `as nickNameHere`.  An
example of importing the pandas library using the common nickname `pd` is below.


~~~
import pandas as pd
~~~
{: .language-python}

Each time we call a function that's in a library, we use the syntax
`LibraryName.FunctionName`. Adding the library name with a `.` before the
function name tells Python where to find the function. In the example above, we
have imported Pandas as `pd`. This means we don't have to type out `pandas` each
time we call a Pandas function.


# Reading CSV Data Using Pandas

We will begin by locating and reading our wave data which are in CSV format. CSV stands for
Comma-Separated Values and is a common way to store formatted data. Other symbols may also be used, so
you might see tab-separated, colon-separated or space separated files. It is quite easy to replace
one separator with another, to match your application. The first line in the file often has headers
to explain what is in each column. CSV (and other separators) make it easy to share data, and can be
imported and exported from many applications, including Microsoft Excel. For more details on CSV
files, see the [Data Organisation in Spreadsheets][spreadsheet-lesson5] lesson.
We can use Pandas' `read_csv` function to pull the file directly into a [DataFrame][pd-dataframe].

## So What's a DataFrame?

A DataFrame is a 2-dimensional data structure that can store data of different
types (including characters, integers, floating point values, factors and more)
in columns. It is similar to a spreadsheet or an SQL table or the `data.frame` in
R. A DataFrame always has an index (0-based). An index refers to the position of
an element in the data structure.

~~~
# Note that pd.read_csv is used because we imported pandas as pd
pd.read_csv("data/waves.csv")
~~~
{: .language-python}

The above command yields the **output** below:

~~~
      record_id month day year  plot_id species_id  sex hindfoot_length weight
0             1     7  16 1977        2         NL    M            32.0    NaN
1             2     7  16 1977        3         NL    M            33.0    NaN
2             3     7  16 1977        2         DM    F            37.0    NaN
3             4     7  16 1977        7         DM    M            36.0    NaN
4             5     7  16 1977        3         DM    M            35.0    NaN
...         ...   ... ...  ...      ...        ...  ...             ...    ...
35544     35545    12  31 2002       15         AH  NaN             NaN    NaN
35545     35546    12  31 2002       15         AH  NaN             NaN    NaN
35546     35547    12  31 2002       10         RM    F            15.0   14.0
35547     35548    12  31 2002        7         DO    M            36.0   51.0
35548     35549    12  31 2002        5        NaN  NaN             NaN    NaN

[35549 rows x 9 columns]
~~~
{: .output}

We can see that there were 35,549 rows parsed. Each row has 9
columns. The first column is the index of the DataFrame. The index is used to
identify the position of the data, but it is not an actual column of the DataFrame.
It looks like  the `read_csv` function in Pandas  read our file properly. However,
we haven't saved any data to memory so we can work with it. We need to assign the
DataFrame to a variable. Remember that a variable is a name for a value, such as `x`,
or  `data`. We can create a new  object with a variable name by assigning a value to it using `=`.

Let's call the imported wave data `waves_df`:

~~~
waves_df = pd.read_csv("data/waves.csv")
~~~
{: .language-python}

Notice when you assign the imported DataFrame to a variable, Python does not
produce any output on the screen. We can view the value of the `waves_df`
object by typing its name into the Python command prompt.

~~~
waves_df
~~~
{: .language-python}

which prints contents like above.

Note: if the output is too wide to print on your narrow terminal window, you may see something
slightly different as the large set of data scrolls past. You may see simply the last column
of data:
~~~
17        NaN
18        NaN
19        NaN
20        NaN
21        NaN
22        NaN
23        NaN
24        NaN
25        NaN
26        NaN
27        NaN
28        NaN
29        NaN
...       ...
35519    36.0
35520    48.0
35521    45.0
35522    44.0
35523    27.0
35524    26.0
35525    24.0
35526    43.0
35527     NaN
35528    25.0
35529     NaN
35530     NaN
35531    43.0
35532    48.0
35533    56.0
35534    53.0
35535    42.0
35536    46.0
35537    31.0
35538    68.0
35539    23.0
35540    31.0
35541    29.0
35542    34.0
35543     NaN
35544     NaN
35545     NaN
35546    14.0
35547    51.0
35548     NaN

[35549 rows x 9 columns]
~~~
{: .output}

Never fear, all the data is there, if you scroll up. Selecting just a few rows, so it is
easier to fit on one window, you can see that pandas has neatly formatted the data to fit
our screen:

~~~
waves_df.head() # The head() method displays the first several lines of a file. It
                  # is discussed below.
~~~
{: .language-python}
~~~
   record_id  month  day  year  plot_id species_id sex  hindfoot_length  \
5          6      7   16  1977        1         PF   M             14.0
6          7      7   16  1977        2         PE   F              NaN
7          8      7   16  1977        1         DM   M             37.0
8          9      7   16  1977        1         DM   F             34.0
9         10      7   16  1977        6         PF   F             20.0

   weight
5     NaN
6     NaN
7     NaN
8     NaN
9     NaN
~~~
{: .output}

## Exploring Our Wave Buoy Data

Again, we can use the `type` function to see what kind of thing `waves_df` is:

~~~
type(waves_df)
~~~
{: .language-python}
~~~
<class 'pandas.core.frame.DataFrame'>
~~~
{: .output}

As expected, it's a DataFrame (or, to use the full name that Python uses to refer
to it internally, a `pandas.core.frame.DataFrame`).

What kind of things does `waves_df` contain? DataFrames have an attribute
called `dtypes` that answers this:

~~~
waves_df.dtypes
~~~
{: .language-python}
~~~
record_id            int64
month                int64
day                  int64
year                 int64
plot_id              int64
species_id          object
sex                 object
hindfoot_length    float64
weight             float64
dtype: object
~~~
{: .output}

All the values in a column have the same type. For example, buoy_id have type
`int64`, which is a kind of integer. Cells in the month column cannot have
fractional values, but the TPeak and Wave Height columns can, because they
have type `float64`. The `object` type doesn't have a very helpful name, but in
this case it represents strings (such as 'Coastal' and 'Ocean' in the case of Site Type).

We'll talk a bit more about what the different formats mean in a different lesson.

### Useful Ways to View DataFrame Objects in Python

There are many ways to summarize and access the data stored in DataFrames,
using **attributes** and **methods** provided by the DataFrame object.

To access an attribute, use the DataFrame object name followed by the attribute
name `df_object.attribute`. Using the DataFrame `waves_df` and attribute
`columns`, an index of all the column names in the DataFrame can be accessed
with `waves_df.columns`.

Methods are called in a similar fashion using the syntax `df_object.method()`.
As an example, `waves_df.head()` gets the first few rows in the DataFrame
`waves_df` using the `head()` method. With a method we can supply extra
information in the parens to control behaviour.

Let's look at the data using these.

> ## Challenge - DataFrames
>
> Using our DataFrame `waves_df`, try out the **attributes** & **methods** below to see
> what they return.
>
> 1. `waves_df.columns`
> 2. `waves_df.shape` Take note of the output of `shape` - what format does it
>    return the shape of the DataFrame in?
>
>    HINT: [More on tuples, here][python-datastructures].
> 3. `waves_df.head()` Also, what does `waves_df.head(15)` do?
> 4. `waves_df.tail()`
{: .challenge}


## Calculating Statistics From Data In A Pandas DataFrame

We've read our data into Python. Next, let's perform some quick summary
statistics to learn more about the data that we're working with. We might want
to know how many observations were collected in each site, or how many observations
were made in each country. We can perform summary stats quickly using groups. But
first we need to figure out what we want to group by.

Let's begin by exploring our data:

~~~
# Look at the column names
waves_df.columns
~~~
{: .language-python}

which **returns**:

~~~
Index(['record_id', 'month', 'day', 'year', 'plot_id', 'species_id', 'sex',
       'hindfoot_length', 'weight'],
      dtype='object')
~~~
{: .output}

Let's get a list of all the buoys. The `pd.unique` function tells us all of
the unique values in the `Name` column.

~~~
pd.unique(waves_df['Name'])
~~~
{: .language-python}

which **returns**:

~~~
array(['NL', 'DM', 'PF', 'PE', 'DS', 'PP', 'SH', 'OT', 'DO', 'OX', 'SS',
       'OL', 'RM', nan, 'SA', 'PM', 'AH', 'DX', 'AB', 'CB', 'CM', 'CQ',
       'RF', 'PC', 'PG', 'PH', 'PU', 'CV', 'UR', 'UP', 'ZL', 'UL', 'CS',
       'SC', 'BA', 'SF', 'RO', 'AS', 'SO', 'PI', 'ST', 'CU', 'SU', 'RX',
       'PB', 'PL', 'PX', 'CT', 'US'], dtype=object)
~~~
{: .output}

> ## Challenge - Statistics
>
> 1. Create a list of unique site ID's ("buoy_id") found in the waves data. Call it
>   `site_names`. How many unique sites are there in the data? How many unique
>   buoys are in the data?
>
> 2. What is the difference between `len(site_names)` and `waves_df['buoy_id'].nunique()`?
{: .challenge}

# Groups in Pandas

We often want to calculate summary statistics grouped by subsets or attributes
within fields of our data. For example, we might want to calculate the average
temperature at all buoys per Site Type.

We can calculate basic statistics for all records in a single column using the
syntax below:

~~~
waves_df['Temperature'].describe()
~~~
{: .language-python}
gives **output**

~~~
count    32283.000000
mean        42.672428
std         36.631259
min          4.000000
25%         20.000000
50%         37.000000
75%         48.000000
max        280.000000
Name: Temperature, dtype: float64
~~~
{: .language-python}

We can also extract one specific metric if we wish:

~~~
waves_df['Temperature'].min()
waves_df['Temperature'].max()
waves_df['Temperature'].mean()
waves_df['Temperature'].std()
waves_df['Temperature'].count()
~~~
{: .language-python}

But if we want to summarize by one or more variables, for example Site Type, we can
use **Pandas' `.groupby` method**. Once we've created a groupby DataFrame, we
can quickly calculate summary statistics by a group of our choice.

~~~
# Group data by Site Type
grouped_data = waves_df.groupby('Site Type')
~~~
{: .language-python}

The **pandas function `describe`** will return descriptive stats including: mean,
median, max, min, std and count for a particular column in the data. Pandas'
`describe` function will only return summary values for columns containing
numeric data.

~~~
# Summary statistics for all numeric columns by Site Type
grouped_data.describe()
# Provide the mean for each numeric column by Site Type
grouped_data.mean()
~~~
{: .language-python}

`grouped_data.mean()` **OUTPUT:**

~~~
        record_id     month        day         year    plot_id  \
sex
F    18036.412046  6.583047  16.007138  1990.644997  11.440854
M    17754.835601  6.392668  16.184286  1990.480401  11.098282

     hindfoot_length     weight
sex
F          28.836780  42.170555
M          29.709578  42.995379

~~~
{: .output}

The `groupby` command is powerful in that it allows us to quickly generate
summary stats.

> ## Challenge - Summary Data
>
> 1. How many buoys are 'Coastal' and how many `Ocean`?
> 2. What happens when you group by two columns using the following syntax and
>    then calculate mean values?
>   - `grouped_data2 = waves_df.groupby(['buoy_id', 'Site Type'])`
>   - `grouped_data2.mean()`
> 3. Summarize Temperature values for each site in your data. HINT: you can use the
>   following syntax to only create summary statistics for one column in your data.
>   `by_site['Temperature'].describe()`
>
>
>> ## Did you get #3 right?
>> **A Snippet of the Output from challenge 3 looks like:**
>>
>> ~~~
>>  site
>>  1     count    1903.000000
>>        mean       51.822911
>>        std        38.176670
>>        min         4.000000
>>        25%        30.000000
>>        50%        44.000000
>>        75%        53.000000
>>        max       231.000000
>>          ...
>> ~~~
>> {: .output}
> {: .solution}
{: .challenge}

## Quickly Creating Summary Counts in Pandas

Let's next count the number of samples for each Country. We can do this in a few
ways, but we'll use `groupby` combined with **a `count()` method**.


~~~
# Count the number of samples by Country
country_counts = waves_df.groupby('Country')['record_id'].count()
print(country_counts)
~~~
{: .language-python}

Or, we can also count just the rows that have the Country "Wales":

~~~
waves_df.groupby('Country')['record_id'].count()['Wales']
~~~
{: .language-python}

> ## Challenge - Make a list
>
>  What's another way to create a list of countries and associated `count` of the
>  records in the data? Hint: you can perform `count`, `min`, etc. functions on
>  groupby DataFrames in the same way you can perform them on regular DataFrames.
{: .challenge}

## Basic Math Functions

If we wanted to, we could perform math on an entire column of our data. For
example let's multiply all Temperature values by 2. A more practical use of this might
be to normalize the data according to a mean, area, or some other value
calculated from our data.

~~~
# Multiply all Temperature values by 2
waves_df['Temperature']*2
~~~
{: .language-python}

# Quick & Easy Plotting Data Using Pandas

We can plot our summary stats using Pandas, too.

~~~
# Make sure figures appear inline in Ipython Notebook
%matplotlib inline
# Create a quick bar chart
country_counts.plot(kind='bar');
~~~
{: .language-python}

![Weight by Species Site](../fig/countPerSpecies.png)
Count per species site

We can also look at how many observations were made at each site:

~~~
total_count = waves_df.groupby('buoy_id')['record_id'].nunique()
# Let's plot that too
total_count.plot(kind='bar');
~~~
{: .language-python}

> ## Challenge - Plots
>
> 1. Create a plot of average Temperature across all buoy_id per Site Type.
> 2. Create a plot of total Coastal versus total Ocean sites for the entire dataset.
{: .challenge}

> ## Summary Plotting Challenge
>
> Create a stacked bar plot, with Temperature on the Y axis, and the stacked variable
> being Site Type. The plot should show average Temperature by Site Type for each site. Some
> tips are below to help you solve this challenge:
>
> * For more information on pandas plots, see [pandas' documentation page on visualization][pandas-plot].
> * You can use the code that follows to create a stacked bar plot but the data to stack
>  need to be in individual columns.  Here's a simple example with some data where
>  'a', 'b', and 'c' are the groups, and 'one' and 'two' are the subgroups.
>
> ~~~
> d = {'one' : pd.Series([1., 2., 3.], index=['a', 'b', 'c']), 'two' : pd.Series([1., 2., 3., 4.], index=['a', 'b', 'c', 'd'])}
> pd.DataFrame(d)
> ~~~
> {: .language-python }
>
> shows the following data
>
> ~~~
>       one  two
>   a    1    1
>   b    2    2
>   c    3    3
>   d  NaN    4
> ~~~
> {: .output}
>
> We can plot the above with
>
> ~~~
> # Plot stacked data so columns 'one' and 'two' are stacked
> my_df = pd.DataFrame(d)
> my_df.plot(kind='bar', stacked=True, title="The title of my graph")
> ~~~
> {: .language-python }
>
> ![Stacked Bar Plot](../fig/stackedBar1.png)
>
> * You can use the `.unstack()` method to transform grouped data into columns
> for each plotting.  Try running `.unstack()` on some DataFrames above and see
> what it yields.
>
> Start by transforming the grouped data (by site and country) into an unstacked layout, then create
> a stacked plot.
>
>
>> ## Solution to Summary Challenge
>>
>> First we group data by site and by country, and then calculate a total for each site.
>>
>> ~~~
>> by_site_country = waves_df.groupby(['buoy_id', 'Country'])
>> site_country_count = by_site_country['Temperature'].sum()
>> ~~~
>> {: .language-python}
>>
>> This calculates the sums of Temperature for each country within each site as a table
>>
>> ~~~
>> site  country
>> buoy_id  sex
>> 1        F      38253
>>          M      59979
>> 2        F      50144
>>          M      57250
>> 3        F      27251
>>          M      28253
>> 4        F      39796
>>          M      49377
>> <other sites removed for brevity>
>> ~~~
>> {: .output}
>>
>> Below we'll use `.unstack()` on our grouped data to figure out the total Temperature that each country contributed to each site.
>>
>> ~~~
>> by_site_country = waves_df.groupby(['buoy_id', 'Country'])
>> site_country_count = by_site_country['Temperature'].sum()
>> site_country_count.unstack()
>> ~~~
>> {: .language-python }
>>
>> The `unstack` method above will display the following output:
>>
>> ~~~
>> sex          F      M
>> buoy_id
>> 1        38253  59979
>> 2        50144  57250
>> 3        27251  28253
>> 4        39796  49377
>> <other sites removed for brevity>
>> ~~~
>> {: .output}
>>
>> Now, create a stacked bar plot with that data where the temperatures for each Country are stacked by site.
>>
>> Rather than display it as a table, we can plot the above data by stacking the values of each country as follows:
>>
>> ~~~
>> by_site_country= waves_df.groupby(['buoy_id', 'Country'])
>> site_country_count = by_site_country['Temperature'].sum()
>> spc = site_country_count.unstack()
>> s_plot = spc.plot(kind='bar', stacked=True, title="Total Temperature by site and country")
>> s_plot.set_ylabel("Temperature")
>> s_plot.set_xlabel("Plot")
>> ~~~
>> {: .language-python}
>>
>> ![Stacked Bar Plot](../fig/stackedBar.png)
> {: .solution}
{: .challenge}

[ernst]: http://www.esapubs.org/archive/ecol/E090/118/default.htm
[figshare-ndownloader]: https://ndownloader.figshare.com/files/2292172
[os-lib]: https://docs.python.org/3/library/os.html
[matplotlib]: https://matplotlib.org
[numpy]: https://www.numpy.org/
[pandas]: https://pandas.pydata.org
[pandas-plot]: http://pandas.pydata.org/pandas-docs/stable/user_guide/visualization.html#basic-plotting-plot
[pd-dataframe]: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html
[pptd]: https://figshare.com/articles/Portal_Project_Teaching_Database/1314459
[python-datastructures]: https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences
[spreadsheet-lesson5]: http://www.datacarpentry.org/spreadsheet-ecology-lesson/05-exporting-data

{% include links.md %}

