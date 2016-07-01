---
layout: post
title:  Let's make a Facebook Messenger bot!
date:   2016-06-20 00:41:45
description: Tutorial to make a simple Facebook Messender bot using Flask
categories: Learning
tags:
  - Facebook
  - Bot
  - Python
  - API
permalink: making-facebook-bot
---


I love talking with Poncho. He is witty, quirky, and always seems to know about the weather. He is not a human though, he is a weathercat. Err, I mean he is a Facebook Messenger bot. Like all bots (or robots), he has a very important job. He gives people the forecast of local weather. Let's make our very own bot to see how Poncho works.
<!--more-->

ATTENTION: You need to have a little bit idea of Python to follow this tutorial.

How it works:
Many things created by Facebook is usually accessible by their API. Same goes for the messenger bots. To use their API you would need a Facebook app. When you create an app on Facebook, you are given a app ID and app secret, which are kind of similar to they way we use username and password to log on to Facebook.

Although apps can do many things including access an user's profile information, posts, pictures etc, they can't really send message to the users. I mean it wouldn't be normal to chat with a Facebook app either. On the other hand, Facebook pages can send and recieve messages from users, since sometimes Pages are intended to represent people or fictional characters. Facebook bots are basically apps that chat with users by messeging through pages.

Of course an app can't reply to the messages on its own, so we handle the what-to-reply part with our own app. And that's all we need to worry about.

# Steps:

### Step 1. Create a Facebook Page
First of all, [create](https://www.facebook.com/pages/create) a Facebook page that will chat with users. I chose "Fictional Character" from "Artist, Band or Public Figure" category and named it "Katy the Kitten".

### Step 2. Create a Facebook App
[Create](https://developers.facebook.com/quickstarts/) a Facebook App to access Facebook's API. Simply click on "basic setup", give a name for your chatbot, a contact email and select a category (any will do). I named my app as "Katy Bot".

[image 1]

### Step 3. Add Messenger to app
From your app's dashboard, click on "Add Product" button in your app's dashboard's lower left and add Messenger. In the Messenger settings page, add the page you created in step 1. Don't worry when Facebook asks for permission, just continue till you get a "Page Access Token". You will need it later.

[image 3]

### Step 4. Connect to Facebook's API!
Now it's time to work on the inner mechanisms of our bot. Let's get on with it!

Some explanation before proceeding. You will need to add a webhook to your app. A webhook is basically an HTTP callback. When you receive a message in your Facebook page, you would want to get notified of the event. From Messenger settings click on the "Setup Webhooks" button:

[image 4]

Let's make a Callback URL first. Here is a basic Flask app "bot.py" that has only one end point called 'webhook'. Type any string for `VERIFY_TOKEN`.

{% highlight python %}
from flask import Flask, request
app = Flask(__name__)

VERIFY_TOKEN = 'this_is_a_token'

@app.route('/webhook', methods=['GET', 'POST'])
def webhook():
    if request.args.get('hub.verify_token') == VERIFY_TOKEN:
      return request.args.get('hub.challenge')
    return "Wrong Verify Token"


if __name__ == '__main__':
    app.run()

{% endhighlight %}

Run `python bot.py` in terminal and try to access `http://127.0.0.1:5000/webhook`. You will see "Wrong Verify Token", because you aren't supplying any token at the moment. This Flask app actually can work with Facebook just fine, only it's in your localhost (i.e. `http://127.0.0.1:5000/`) and Facebook can't access it. Download and install [ngrok](https://ngrok.com/
) to "tunnel" this address using `ngrok`. While the Flask app is running, use this command in another terminal:

{% highlight bash %}
ngrok http 5000
{% endhighlight %}

You will see a bunch of messages and an URL that looks something like `https://26af113d.ngrok.io`. ngrok has exposed your server to the world using this address. Now the full Callback URL is `https://26af113d.ngrok.io/webhook` (notice the `https` portion). Set this URL with in the "Setup Webhooks" page and the same verify token in the Flask app. Select all the Subscription Fields and hit "Verify and Save."

Now our Flask app is ready to send and receive messages!


### Step 5: Receive messages
If you think carefully, the initial verificatoin process was only a HTTP GET event, we did not really receive any information. To start receiving messages, we need to be able to do that. catch the POST events. Use a if-else condition to catch POST events separately than GET events:

{% highlight python %}
from flask import Flask, request
app = Flask(__name__)

VERIFY_TOKEN = 'this_is_a_token'

@app.route('/webhook', methods=['GET', 'POST'])
def webhook():
  if request.method == 'POST':
    data = json.loads(request.data)
  elif request.method == 'GET': # For the initial verification
    if request.args.get('hub.verify_token') == VERIFY_TOKEN:
      return request.args.get('hub.challenge')
    return "Wrong Verify Token"


if __name__ == '__main__':
    app.run()
{% endhighlight %}

Here whenever your app gets a POST request, it gets saved to `data` in JSON format.


### Step 6: Send messages

Step 2. Set username for your bot.
If you want to give out people a short URL to chat with your bot, then you will need to give the page a nice and easy username. Click on "Create Page @username" when in your bot's page and create the username.
