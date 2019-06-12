
[Source](https://medium.com/google-cloud/twitter-analytics-part-2-f282c49c6de7 "Permalink to Twitter Analytics (Part 2) – Google Cloud Platform")

# Twitter Analytics (Part 2) – Google Cloud Platform

_This is the second part of the article ( to see how this story begins visit : ____ )_

### Second Step: Analyze the Data

#### 1) Google Datalab

Google Datalab offers the possibility of easy access the data in BQ and analyze it in a Jupiter notebook. We can also use a large number of existing packages for statistics, machine learning and data processes. It's also easy to deploy within the Google Cloud platform : 

First thing to do is make sure you have all the packages installed in your project, I mean Pandas, Seaborn, Numpy, Matplotlib and Scikit-learn (just needed if you want to make some ML project). If not, you can allways install or update a packege openning a notebook and typing:

> _!pip install lib-name_

Second you have to make the data accessible into the notebook. This we can do it like this:

> _%%sql — module data_name -d standard  
#SQL query  
SELECT  
 created_normalize,  
 text  
FROM  
 [table_name]_

And then fetch the data by coding in python

> _import datalab.bigquery as bq  
my_data_frame = bq.Query(data_name).to_dataframe()_

As you can see in this table i have a field named "created_normalize" that is because I process the data first in python to normalize the dates.

Ok so now i have a data frame with the result of the query what next? I started by change dates into a time line in minutes with the start of the match as 0.

> min_datetime = my_data_frame['created_normalize'].min()  
difference = 20  
my_data_frame['time_line'] = [ int( ( ( x — min_datetime ).total_seconds())/60)-difference for x in my_data_frame['created_normalize']]

Note I used a difference of 20, that's because I started recording 20 minutes before the match starts.

This added one new column to the data set 'time_line', which let us easily plot the amount of tweets for minutes for example.

> _my_data_frame['tweets'] = my_data_frame['time_line']  
ax = sns.kdeplot(my_data_frame['tweets'], shade=True)  
ax.set(xlabel='minutes', ylabel='density',title='Tweet's distribution')  
plt.axvline(x=0, color='g')  
plt.axvline(x=47, color='g')  
plt.axvline(x=66, color='g')  
plt.axvline(x=71, color='r')  
plt.axvline(x=74, color='b')  
plt.axvline(x=115, color='g')  
plt.show()_

![][1]

Argentina — Venezuela (2017/09/05)

Green lines represent the start/end of each half, red is a goal of Venezuela and blue line represents a goal of Argentina.

Or we can look for mentions of each Argentinian player.

![][2]

We can do much more with this data, the rest I leave it to you…

_If you want the data set of these two matches I will make it public for everybody._

[1]: https://cdn-images-1.medium.com/max/1600/1*hmkw2J172OaJIqiSJ8wa8Q.png
[2]: https://cdn-images-1.medium.com/max/1600/1*u5mTndoS8mD9lNdwPX_AcA.png

  
