---
layout:     post
title:      "পাইথন দিয়ে গিটহাবের ডেটা স্ক্র্যাপিং - পর্ব ১"
permalink: BD
date:       2015-07-08 00:31:19
author:     Ratul Minhaz
summary:    "পাইথনের বেসিক ব্যবহার করে সহজে ডেটা স্ক্রেপিং শেখা যায়।"
categories: learning
thumbnail: heart
tags:
 - scraping
 - python
 - API
published: true

---

<small>
  একটুখানি পাইথন জানা থাকতে হবে পোস্টটা বুঝতে :( না জানা থাকলে সরি। <a href="http://www.codecademy.com/en/tracks/python">কোডঅ্যাকাডেমিতে</a> পাইথনের ট্র্যাকটা থেকে বরং ঘুরে আসুন, কেমন?
</small>


## উপকরণ
- পাইথন ৩
- রিথিংকডিবি ২.০.x (দ্বিতীয় পর্বের জন্য)
- এক চিমটি পাইথন জানা থাকা লাগবে


## শুরু
প্রতিদিন কমসে কম ১০টি নতুন সাইটের দেখা পাই, এ কথা আমি নির্দ্বিধায় বলে দিতে পারি। অবশ্য যা খুঁজতে এসেছিলাম তা পেলেই বন্ধ করে দেই, এত এত সাইটের শত শত তথ্য পড়ার টাইম কই! কিন্তু এই তথ্যগুলো যদি আমরা সংগ্রহ করে নিতে পারতাম, তাহলে কেমন হত?

গিটহাব, ফেসবুক ইত্যাদি সাইটের API ব্যবহার করে একটা মজার কাজ করা যায়, তা হল, যে কোন ইউজারের পাবলিক তথ্য বের করে ফেলা যায় অনায়াসে। উদাহরণ দেই আমার নিজের গিটহাব প্রোফাইল ব্যবহার করে। আমার ইউজারনেম ওখানে `mnzr`, তাই আপনাকে যেতে হবে এই ঠিকানাটায়: [https://api.github.com/users/mnzr](https://api.github.com/users/mnzr). এই পাতাটায় গেলে আপনি এমন কিছু একটা দেখতে পাবেন:
{% highlight json %}
{
  "login": "mnzr",
  "id": 1234482,
  "avatar_url": "https://avatars.githubusercontent.com/u/1234482?v=3",
  "gravatar_id": "",
  "url": "https://api.github.com/users/mnzr",
  "html_url": "https://github.com/mnzr",
  ... ... ...
  ... ... ...
  "type": "User",
  "site_admin": false,
  "name": "Ratul Minhaz",
  "company": "",
  "blog": "",
  "location": "Dhaka, Bangladesh",
  "email": "minhaz.ratul@gmail.com",
  "hireable": false,
  "bio": null,
  "public_repos": 31,
  "public_gists": 7,
  "followers": 15,
  "following": 35,
  "created_at": "2011-12-01T20:29:31Z",
  "updated_at": "2015-06-24T18:56:46Z"
}
{% endhighlight %}

সহজে পড়ার খাতিরে ... দিয়ে কয়েকটা লাইন বাদ দিয়েছি। খেয়াল করলে দেখবেন গিটহাবে আমাকে নিয়ে যত তথ্য আছে তার সবই চিচিংফাক করে দিয়েছে ওয়েব পেজটা। আমি কবে জয়েন করেছি, সর্বসাকুল্যে কয়খানা পাবলিক রেপো আছে আমার, এমন কি কতজন ফলোয়ার-ফলোয়িং সবই দেখা যাচ্ছে। এখন ভেবে দেখুন তো, এই তথ্য নিয়ে আপনি কি 'না' করতে পারবেন! :D থাক অত বেশি ভাবতে হবে না। আমরা বরং দেখি কিভাবে এই তথ্য হাতিয়ে নেয়া যায় নিঃস্বার্থ বন্ধু পাইথন দিয়ে।


## কাদের তথ্য নিব?
আপনার যাকে মনে ধরে! যদি কেউকে মনে না ধরে তাহলে [এখানে](https://github.com/trending/developers) গিয়ে দেখে আসুন আজকে সারাবেলা গিটহাব জুড়ে কাদের দাপট্য বেশি ছিল। তাদের ইউজারনেমগুলো নিয়ে একটা পাইথন ফাইলে লিস্ট করে রেখে দিন। আর সাথে লিখে রাখুন গিটহাবের API এর ঠিকানাটা[^1]। এর ঠিক কোন অ্যাড্রেসটায় গেলে কোন ইউজারের এসব তথ্যগুলোর দেখা পাওয়া যায়। আর আরেকটা লিস্ট বানাব যেটায় থাকবে ঠিক কি কি তথ্য আমরা নিতে চাই সেগুলোর নাম বা key.
{% highlight python %}
users = ['google','facebook','apache']
url = "https://api.github.com/users/"
properties = ['login', 'id', 'html_url', 'public_repos', 'created_at']
{% endhighlight %}
একেকজন ইউজারের URL তৈরী হবে users থেকে একটা ভ্যালু নিয়ে url এর শেষে যোগ করে। আমরা শুধু এই তিন ইউজারের তথ্য নিয়েই আপাতত মিলাব।


## তথ্যগুলো নিব কিভাবে?
ব্যস, কাদের কাদের তথ্য আমরা নিতে চাই, কোন ইউআরএল ব্যবহার করে নিতে চাই, এবং কি কি তথ্য নিতে চাই এগুলো ঠিক করা হয়ে গেল। কিন্তু কিভাবে যে নিব সেটাই বলা হল না।
আমরা এমনিতে কোন সাইট দেখতে চাইলে প্রথমে ব্রাউজার খুলি, তারপর সাইটের অ্যাড্রেস দেই, তারপর সেটা আমাদের কাছে আসে। পাইথনের এত ঝামেলা নেই, সে একটামাত্র মডিউল ইম্পোর্ট করেই এই কাজ সারতে পারে: ‍`requests‍`। এই মডিউলটায় যে কোন ওয়েব পেজ ধরে বিভিন্ন ফরমেটে কনভার্ট করা যায়। মডিউলটি ইন্সটল না করা থাকলে `pip install requests` কমান্ড চালিয়ে ইনস্টল করে নিন।  ধরে নিন আমরা যে পেজটা পাচ্ছি সেটাকে `response` নামে সেভ করব।
{% highlight python %}
response = requests.get(current_page)
{% endhighlight %}
আমরা যখন একটা ওয়েব পেজ দেখি ব্রাউজারে, সেটা আসলে ব্রাউজারের কারসাজি তা আমরা সবাই কম বেশি জানি। সত্যি সত্যি পিসি থেকে কোন ওয়েবসাইটে রিকোয়েস্ট গেলে আসলে যে রিপ্লাই আসে, তা একটা HTTP কোড(একটা কোড আমরা সবাই চিনি, Error: 404!), কিছু তথ্য, এবং সাথে আমাদের রিকোয়েস্ট করা তথ্য। এই HTTP রিপ্লাইটাকে পাইথন দেখে একটা অবজেক্ট হিসাবে। তাই রিপ্লাইটা থেকে আমাদের দরকারি `json` ফরমেটের তথ্যটা আলাদা করতে আরও কিছু করতে হবে।
{% highlight python %}
json = response.json()
{% endhighlight %}
ব্যস, ব্রাউজারে কারও ইউজারনেম দিয়ে https://api.github.com/users/ ঠিকানাটায় গেলে যেই রিপ্লাই আসে, আমরা এখন সেই রিপ্লাইটাকে `json` এ ধরে রাখতে পারি!


## কি তথ্য পেলাম আসলে?
তথ্য যা পেলাম সব জমা হয়ে গেছে `json` ডিকশনারীটাতে। সাধারণত ডিকশনারি যেভাবে টার্মিনালে প্রিন্ট করা যায় এ ক্ষেত্রেও একইভাবে প্রিন্ট করুন:
{% highlight python %}
for prop in properties:
            print prop, ':', json[prop]
        print ''

{% endhighlight %}
এখানে যা হচ্ছে তা হল, আমাদের `properties` লিস্ট থেকে একটা একটা করে প্রোপার্টি দেখানো হচ্ছে `json` নামে প্রতিটি অবজেক্টের জন্য।
এটা রান করলে আপনার স্ক্রিনে দেখাবে:
‍‍‍
{% highlight text %}
login : google
id : 1342004
html_url : https://github.com/google
public_repos : 532
created_at : 2012-01-18T01:30:18Z
{% endhighlight %}


তাহলে পুরো কোডটা দাঁড়াচ্ছে এমন:

{% highlight python %}
#!/usr/bin/env python
"""Script to get Github info using API"""
import requests


def main():
    # Add any number of usernames here
    users = ['google', 'facebook', 'apache']
    url = "https://api.github.com/users/"
    # Which JSON properties we want to catch
    properties = ['login', 'id', 'html_url', 'public_repos', 'created_at']

    for user in users:
        # make API url with Github username
        current_page = url + user
        # take response from the url
        response = requests.get(current_page)
        # conversion to human readable format
        json = response.json()
        for prop in properties:
            print prop, ':', json[prop]
        print ''


if __name__ == '__main__':
    main()
{% endhighlight %}
এই পর্বটি ভাল লেগে থাকলে কিংবা কোন মতামত থাকলে অবশ্যই জানাবেন :) দ্বিতীয় পর্ব সামনে আসছে।


[^1]: ইউজার ছাড়া অন্যান্য API ঠিকানা জানতে: [API](https://developer.github.com/v3/)
