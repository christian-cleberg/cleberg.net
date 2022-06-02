+++
date = 2021-08-25
title = "Audit Sampling with Python"
description = "Learn how to use Python to automate the boring parts of audit sampling."
draft = false
aliases = ["/blog/audit-sampling-with-python"]
+++

## Introduction

For anyone who is familiar with internal auditing, external auditing, or
consulting, you will understand how tedious audit testing can become when you
are required to test large swaths of data. When we cannot establish an automated
means of testing an entire population, we generate samples to represent the
population of data. This helps ensure we can have a small enough data pool to
test and that our results still represent the population.

However, sampling data within the world of audit still seems to confuse quite a
lot of people. While some audit-focused tools have introduced sampling
functionality (e.g. Wdesk), many audit departments and firms cannot use software
like this due to certain constraints, such as the team's budget or knowledge.
Here is where this article comes in - we're going to use
[Python](https://www.python.org), a free and open-source programming language,
to generate random samples from a dataset in order to suffice numerous audit
situations.

## Audit Requirements for Sampling

Before we get into the details of how to sample with Python, I want to make sure
I discuss the different requirements that auditors may have of samples used
within their projects.

### Randomness

First, let's discuss randomness. When testing out new technology to help assist
with audit sampling, you need to understand exactly how your samples are being
generated. For example, if the underlying function is just picking every 57th
element from a list, that's not truly random - it's a systematic form of
sampling. Luckily, since Python is open-source, we have access to its codebase.
Through this blog post, I will be using the [pandas](https://pandas.pydata.org)
module in order to generate the random samples. More specifically, I will be
using the
[pandas.DataFrame.sample](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.sample.html)
function provided by Pandas.

Now that you know what you're using, you can always check out the code behind
`pandas.DataFrame.sample`. This function does a lot of work, but we really only
care about the two following pieces of code:

```python
# Process random_state argument
rs = com.random_state(random_state)

...

locs = rs.choice(axis_length, size=n, replace=replace, p=weights)
result = self.take(locs, axis=axis)
if ignore_index:
result.index = ibase.default_index(len(result))

return result
```

The block of code above shows you that if you assign a `random_state` argument
when you run the function, that will be used as a seed number in the random
generation and will allow you to reproduce a sample, given that nothing else
changes. This is critical to posterity of audit work. After all, how can you say
your audit process is adequately documented if the next person can't run the
code and get the same sample? The final piece here on randomness is to look at
the [choice](https://docs.python.org/3/library/random.html#random.choice)
function used above. This is the crux of the generation and can also be examined
for more detailed analysis on its reliability. As far as auditing goes, we will
trust that these functions are mathematically random.

### Sample Sizes

As mentioned in the intro, sampling is only an effective method of auditing when
it truly represents the entire populations. While some audit departments or
firms may consider certain judgmental sample sizes to be adequate, you may need
to rely on statistically-significant confidence levels of sample testing at
certain points. I will demonstrate both here. For statistically-significant
confidence levels, most people will assume a 90% - 99% confidence level. In
order to actually calculate the correct sample size, it is best to use
statistical tools due to the tedious math work required. For example, for a
population of 1000, and a 90% confidence level that no more than 5% of the items
are nonconforming, you would sample 45 items.

However, in my personal experience, many audit departments and firms do not use
statistical sampling. Most people use a predetermined, often proprietary, table
that will instruct auditors which sample sizes to choose. This allows for
uniform testing and reduces overall workload. See the table below for a common
implementation of sample sizes:

| Control Frequency | Sample Size - High Risk | Sample Size - Low Risk |
| ----------------- | ----------------------- | ---------------------- |
| More Than Daily   | 40                      | 25                     |
| Daily             | 40                      | 25                     |
| Weekly            | 12                      | 5                      |
| Monthly           | 5                       | 3                      |
| Quarterly         | 2                       | 2                      |
| Semi-Annually     | 1                       | 1                      |
| Annually          | 1                       | 1                      |
| Ad-hoc            | 1                       | 1                      |

## Sampling with Python & Pandas

In this section, I am going to cover a few basic audit situations that require
sampling. While some situations may require more effort, the syntax,
organization, and intellect used remains largely the same. If you've never used
Python before, note that lines starting with a '`#`' symbol are called comments,
and they will be skipped by Python. I highly recommend taking a quick tutorial
online to understand the basics of Python if any of the code below is confusing
to you.

### Simple Random Sample

First, let's look at a simple, random sample. The code block below will import
the `pandas` module, load a data file, sample the data, and export the sample to
a file.

```python
# Import the Pandas module
import pandas

# Specify where to find the input file & where to save the final sample
file_input = r'Population Data.xlsx'
file_output = r'Sample.xlsx'

# Load the data with pandas
# Remember to use the sheet_name parameter if your Excel file has multiple sheets
df = pandas.read_excel(file_input)

# Sample the data for 25 selections
# Remember to always use the random_state parameter so the sample can be re-performed
sample = df.sample(n=25, random_state=0)

# Save the sample to Excel
sample.to_excel(file_output)
```

### Simple Random Sample - Using Multiple Input Files

Now that we've created a simple sample, let's create a sample from multiple
files.

```python
# Import the Pandas module
import pandas

# Specify where to find the input file & where to save the final sample
file_input_01 = r'Population Data Q1.xlsx'
file_input_02 = r'Population Data Q2.xlsx'
file_input_03 = r'Population Data Q3.xlsx'
file_output = r'Sample.xlsx'

# Load the data with pandas
# Remember to use the sheet_name parameter if your Excel file has multiple sheets
df_01 = pandas.read_excel(file_input_01)
df_02 = pandas.read_excel(file_input_02)
df_03 = pandas.read_excel(file_input_03)

# Sample the data for 5 selections from each quarter
# Remember to always use the random_state parameter so the sample can be re-performed
sample_01 = df_01.sample(n=5, random_state=0)
sample_02 = df_02.sample(n=5, random_state=0)
sample_03 = df_03.sample(n=5, random_state=0)

# If required, combine the samples back together
sample = pandas.concat([sample_01, sample_02, sample_03], ignore_index=True)

# Save the sample to Excel
sample.to_excel(file_output)
```

### Stratified Random Sample

Well, what if you need to sample distinct parts of a single file? For example,
let's write some code to separate our data by "Region" and sample those regions
independently.

```python
# Import the Pandas module
import pandas

# Specify where to find the input file & where to save the final sample
file_input = r'Sales Data.xlsx'
file_output = r'Sample.xlsx'

# Load the data with pandas
# Remember to use the sheet_name parameter if your Excel file has multiple sheets
df = pandas.read_excel(file_input)

# Stratify the data by "Region"
df_east = df[df['Region'] == 'East']
df_west = df[df['Region'] == 'West']

# Sample the data for 5 selections from each quarter
# Remember to always use the random_state parameter so the sample can be re-performed
sample_east = df_east.sample(n=5, random_state=0)
sample_west = df_west.sample(n=5, random_state=0)

# If required, combine the samples back together
sample = pandas.concat([sample_east, sample_west], ignore_index=True)

# Save the sample to Excel
sample.to_excel(file_output)
```

### Stratified Systematic Sample

This next example is quite useful if you need audit coverage over a certain time
period. This code will generate samples for each month in the data and combine
them all together at the end. Obviously, this code can be modified to stratify
by something other than months, if needed.

```python
# Import the Pandas module
import pandas

# Specify where to find the input file & where to save the final sample
file_input = r'Sales Data.xlsx'
file_output = r'Sample.xlsx'

# Load the data with pandas
# Remember to use the sheet_name parameter if your Excel file has multiple sheets
df = pandas.read_excel(file_input)

# Convert the date column to datetime so the function below will work
df['Date of Sale'] = pandas.to_datetime(df['Date of Sale'])

# Define a function to create a sample for each month
def monthly_stratified_sample(df: pandas.DataFrame, date_column: str, num_selections: int) -> pandas.DataFrame:
    static_num_selections = num_selections final_sample = pandas.DataFrame()
    for month in range(1, 13):
        num_selections = static_num_selections
        rows_list = []
        for index, row in df.iterrows():
            df_month = row[date_column].month
            if month == df_month:
                rows_list.append()
        monthly_df = pd.DataFrame(data=rows_list)
        if (len(monthly_df)) == 0:
            continue
        elif not (len(monthly_df) > sample_size):
            num_selections = sample_size
        elif len(monthly_df) >= sample_size:
            num_selections = sample_size
        sample = monthly_df.sample(n=num_selections, random_state=0)
        final_sample = final_sample.append(sample)
    return sample

# Sample for 3 selections per month
sample_size = 3
sample = monthly_stratified_sample(df, 'Date of Sale', sample_size)
sample.to_excel(file_output)
```

## Documenting the Results

Once you've generated a proper sample, there are a few things left to do in
order to properly ensure your process is reproducible.

1. Document the sample. Make sure the resulting file is readable and includes
   the documentation listed in the next bullet.
2. Include documentation around the data source, extraction techniques, any
   modifications made to the data, and be sure to include a copy of the script
   itself.
3. Whenever possible, perform a completeness and accuracy test to ensure your
   sample is coming from a complete and accurate population. To ensure
   completeness, compare the record count from the data source to the record
   count loaded into Python. To ensure accuracy, test a small sample against the
   source data (e.g. test 5 sales against the database to see if the details are
   accurate).
