
[Source](https://medium.com/faun/writing-a-pub-sub-stream-to-bigquery-401b44c86bf "Permalink to Writing a Pub/Sub Stream to BigQuery – Faun – Medium")

# Writing a Pub/Sub Stream to BigQuery – Faun – Medium

![Go to the profile of Gabriel Fajardo][1]

We're going to explore two important components of the Google Cloud Platform: PubSub and BigQuery.

The application we're going to build writes to BigQuery a twitter stream that's published to a topic in PubSub. The code will be in Python 3.x and it would be helpful to know the basics but following along should not be too hard.

### Topic?

What the heck is a topic? In the Google Cloud there is something called Pub/Sub. It's a messaging system that divides all applications involved into two groups:

A publisher is the application that sends data. In our example, we're going to convert our twitter streaming application into a Publisher. We do this by sending our data to a "topic".

An application subscribed to this topic will get notified whenever there's new data. So the topic is like the middle-man between the Publisher and the Subscriber.

But it gets more interesting.

Think of a topic as a radio antenna. If the radio is turned off, or tuned to an empty frequency, or it's playing music, the antenna is always there.

So if music is being broadcasted on a frequency that no one listens to, the music is still out there, in the air… similarly, a Publisher can send data to a topic that has no Subscriber to receive it.

Or a subscriber can be waiting for data from a topic that isn't getting data sent to it. Like listening to all that static from a bad radio frequency.

And if the Publisher is sending data to a topic that an application is subscribed to then we got some untz! Woo!

So the point is: there can be zero, one, or more publishers, and zero, one or more subscribers related to a topic. And they're completely decoupled, so they're free to break without affecting their counterparts.

### **Creating a Publisher**

Lets get started! We have the data, the [twitter stream][2]. Now it just needs to get sent over to a topic.

So to create a topic on the [Google Cloud Platform Console][3] open the Pub/Sub tab.

![][4]

If you haven't created a topic before then it will look like the image above. Click "Create a topic" and give it a name.

#### Publishing the Twitter Stream

Once the topic is created it's ready to have the tweets published to it. We'll use Google Cloud's Publisher Client for Python. To get it, run: `pip install --upgrade google-cloud-pubsub`

The client library makes calls to the Google Cloud so these calls need to be authenticated. For instructions on how to do that, go [here][5]. Get the GOOGLE_APPLICATION_CREDENTIALS environment variable set up and then come back here.

We're going to modify the twitter_streamer.py file we created last time. To get it go over [here][2]. It'll prepare the class for publishing.
    
    
    **class **TweetStreamListener(StreamListener):  
    **client **= pubsub_v1.PublisherClient()  
    topic_path = client.topic_path(PROJECT_NAME, PUBSUB_TOPIC_NAME)  
    count = 0  
    tweets = []  
    **batch_size **= 50  
    total_tweets = 1000

Hopefully the constants are self explanatory. Place there the corresponding strings. An interesting detail here is the `batch_size` variable. That'll be the number of tweets we wait to collect before publishing them to PubSub.

In the on_status function we're going to include some logic to check when to send the "batch of tweets" to the topic.
    
    
    # on_status function in twitter_streamer.py  
    ...  
    print(tw)  
    self.tweets.append(tw)  
    **if **len(self.tweets) >= self.batch_size:  
    self.write_to_pubsub(self.tweets)  
    self.tweets = []
    
    self.count += 1  
    **if **self.count >= self.total_tweets:  
    **return **False  
    **if **(self.count % 50) == 0:  
    **print**(**"count is: {}"**.format(self.count))  
    **return **True

Finally, to send data to a Pub/Sub topic we need to encode the data to base64. Here's the method that publishes the batch.
    
    
    **def **publish(client, topic_path, data_lines):  
    messages = []  
    **for **line **in **data_lines:  
    messages.append({**'data'**: line})  
    body = {**'messages'**: messages}  
    str_body = json.dumps(body)  
    data = base64.urlsafe_b64encode(bytearray(str_body, **'utf8'**))  
    client.publish(topic_path, data=data)

It's similar to [this ][6]blog post by Google, but I've adapted it for Python version 3.6. If you're using 2.7 then go check that out instead.

[1]: https://cdn-images-1.medium.com/fit/c/100/100/0*d_B3FCmDkDUIlIRJ.
[2]: https://gist.github.com/gfajardo95/f3667f57d0430590de50bb24431b4646
[3]: https://console.cloud.google.com
[4]: https://cdn-images-1.medium.com/max/1600/1*YHYesLQEeU5N1tcjmKh2xQ.png
[5]: https://cloud.google.com/docs/authentication/getting-started
[6]: https://cloud.google.com/solutions/real-time/kubernetes-pubsub-bigquery

  
