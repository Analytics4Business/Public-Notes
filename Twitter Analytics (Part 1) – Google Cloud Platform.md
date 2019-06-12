
[Source](https://medium.com/google-cloud/twitter-analytics-part-1-801c9d494487 "Permalink to Twitter Analytics (Part 1) – Google Cloud Platform")

# Twitter Analytics (Part 1) – Google Cloud Platform

### First Step: Get the data

First thing to do is setting up the environment and start collecting data.

#### 1) Generate the Twitter API credentials.

Go to  and create a new app, then go to "Key and Access Token" and click on "Create my access token". Save the "API Key", "API Secret", "Access Token", "Access Token Secret".

![][1]

#### 2) Create your Google Cloud account.

Google gives us u$s 300 to try the platform, which is more than enough for this project. You have to enter your credit card number to activate your account, but Google assures us they will no charge us until we agree.

![][2]

#### 3) Create your Python script

First install the tweepy package for python () to connect easily to the Twitter Streaming. You can take the library example as guide :  .

#### 4) Create a Compute Engine instance

First Google Cloud will ask you to create a new project, where all your data, and instances are going to be hosted. After that, create an instance where you want to deploy your script, make sure you give permission for access to all the "Google Cloud API" when you create it ( otherwiser you can always edit the instance ). Things to consider are the fact that the smallest type of instance is free ( and Google assures it will keep being free ) without consuming your 300 dollar credit. Another point to consider is the size of the disk you are going to use, if you are having tons of data, maybe you need a bigger disk and not the standard one.

#### 5) Install Google Cloud SDK

Follow the instructions in  to install the SDK into your computer, where you are going to manage the project. This will make it easy for you to connect "Compute Engine" instances via SSH, transfer files to "Cloud Storage" and connect to "BigQuery".

#### 6) Run you script

Once you transfer your brand new script to your Compute Engine instance, you now can run it and store results into a file for analyzing later in "BigQuery". I recommend you to run it on background, if you have the idea of collect data during hours/days/months. For that you can create a daemon :



or just use something like :

`_nohup python -u myscript.py > ./mylog.log 2>&1 &_`

#### 7) Move your data to BigQuery

There are several of ways to send data to "BigQuery", you can stream it in real time, make batches , or the simplest way…

When you have collected all the data needed, stop your script and send the results file to "Data Storage" using the Google Cloud SDK, by simply doing:

_gsutil cp Results.txt gs://my-project.appspot.com/_

Once you have transfered all the data, you can go to BigQuery and create the table as a JSON (New Line delimiter) file.

![][3]

Soon the next part…

[1]: https://cdn-images-1.medium.com/max/1600/1*j355djjpoZRmnyGZHoKojA.jpeg
[2]: https://cdn-images-1.medium.com/max/1600/1*nI97rkr2J03a42Orubf9tw.png
[3]: https://cdn-images-1.medium.com/max/1600/1*Gp2sW3yBVkyPEytW-sMAsQ.png

  
