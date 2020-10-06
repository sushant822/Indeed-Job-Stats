# <center>Indeed-Job-Stats</center>

We all use Indeed for our job search. It is one of the biggest job portal website out there. I always wondered which cities in Canada have the most number of jobs and what is the maximum salary for that position. So I decided to write this program that will take user input for the position he is interested in, search indeed, pull up results and store them in a data frame and display them visually. While writing this program, I faced several problems. Firstly, not all positions have salaries listed for them. Infact, very few of them do so. Secondly, The position for which salaries are listed have them in different formats. For instance, some might have them as hourly wage, some as annual salary, some as monthly etc. So the first challenge was to convert them all to one format. Then, the next thing to do was to take average of all the salaries and use them for the ones that don't have any salary listed.

# <center>WARNING!</center>
### <center>Running this entire code could take a long time!</center>
<hr>

If we look at Indeed's search page, the address looks like:

    https://ca.indeed.com/jobs?q=data+scientist&l=Calgary%2C+AB

So we can substitute 'data+scientist' with user input. The job title has to be all lower case.

Let's start by asking the user which job title he wants search for. First, we create a variable called job_title_1 and store the user input in it

    job_title_1 = input("Enter the Job Title: ")

The way Indeed works is it takes the given user input, converts it to lower case and replaces the spaces with +. So we need to do the same.

Let's convert the job title to lower case using .lower() method.

Then, let's replace the spaces with + using .repalce() method.

And then let's see how the output looks, job_title_3 in this case.

    job_title_2 = job_title_1.lower()
    job_title_3 = job_title_2.replace(' ', '+')

In our case, since we are searching for Data Scientist the output looks good, data+scientist

Now let's write the code that can scrape Indeed.ca with our job_title_3

Here we insert job_title_3 into our url using concatenate feature in python

    url = 'https://www.indeed.ca/jobs?q='+job_title_3+'&l=canada'
    page = requests.get(url)
    soup = BeautifulSoup(page.text, "html.parser")

Now that we have the the url, let's write a function that will parse the url, extract the data and store it in a dataframe.

In order to get a proper data set, we need to parse more than one page. So let's write the code to parse first 10 pages and store them in the same dataframe using pd.concat function.

Once we have the dataset, we can see that it has a lot of stuff that we don't need like \n, $ etc. So let's clean up the data.

When we check the data type of data1, we can see that Salary is stored as an object. In addition to that, Salary is defined as per year, per month, per week and per hour. In order for our analysis to work, we need to have salary displayed as per year and the column salary itself should be either float or int.

Let's sort the rows that don't have any salary mentioned and save them in data_sal_none_temp

    data_sal_none_temp = data1[data1['Salary'].str.contains('None')].reset_index(drop=True)

Now let's bring together our newly formed dataset and save it as <b>data_sal_all</b> and rename the table header as <b>Title</b>, <b>Location</b>, <b>Company</b> and <b>Salary</b>

Now let's replace 'None' values with np.nan and change the data type of Salary to float

    data5a = data_sal_all_1.replace('None',np.nan, regex=True)
    data5a['Salary'].astype('float')

We can now replace the np.nan cells with the mean salary obtained in the previous step.

    data5b = data5a['Salary'].replace(np.nan, mean_1)
    data5ba = data5b.astype('int')

Let's procede with the cleanup of our dataset. Let's drop the cells that don't have any location as it won't be useful to us in our analysis.

    data6a = data5d.replace('None',np.nan, regex=True)
    data6 = data6a[pd.notnull(data5d["Location"])]
    data6 = data6.reset_index(drop=True)
    data7 = data6.groupby('Location').count()
    sort_data = data7.sort_values('Title',ascending=False)

sort_data has a location Canada. We need to drop that row. Since we made our code flexible by asking the user to input his job title, this field might not show up in other job titles. Hence we need to write the code that will check if this field is there, and if it, drop it, else procede without doing anything.

    sort_data_canada_1 = sort_data.reset_index()
    sort_data_canada_2 = sort_data_canada_1.iloc[:, 0]
    sort_data_canada_3 = sort_data_canada_2.isin(['Canada'])
    if sort_data_canada_3.any() == True:
        sort_data_1 = sort_data.drop(['Canada'], axis=0)
    else:
        sort_data_1 = sort_data

Let's split the location into City and Provience

    data1 = sort_data_2['Location'].str.split(',', expand=True)
    data1.columns = ['City', 'Provience']

Merge this new data with the previous data and drop the column Location

    data2 = pd.concat([data1, sort_data_2], axis=1, sort=False)
    data3 = data2.drop(['Location'], axis=1)

