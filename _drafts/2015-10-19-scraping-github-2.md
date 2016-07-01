---
layout: post
title: "Scraping Github - Fetching Data (Part 2)"
permalink: scraping-github-2
date: 2015-10-19
author: Ratul Minhaz
summary: Learn to scrape data from API using Python
categories: Learning
tags:
  - scraping
  - python
  - API
published: true
---

API, or Application Programming Interface was created to make things easier for developers who want to use anything made by other people. That's the watered down version of what an API is, you can(should) Google and learn more about it. There are tons of APIs out there that you can use to access anything starting from social media posts to map location data. A site called [Programmable Web](http://www.programmableweb.com/) keeps track of over 13,000 APIs like Google Maps, Twitter, Youtube, Flickr etc.
<!--more-->

*This is the second of a three part tutorial series on getting started with scraping data from Github:*<br>
*1. [The Setup]({{ site.url}}/scraping-github-1)*<br>
*2. [Fetching Data]({{ site.url}}/scraping-github-2) (<-- you are here)*<br>
*3. [Storing to RethinkDB]({{ site.url}}/scraping-github-3)*<br>

Are you ready to begin scraping? Definitely you are, you have set up all the tools needed. But the question is, what are you going to scrape? Remember the API I talked about last time?

# Exploring Github's API
Websites like Github and Facebook have API or Application Programming Interface so that you can interact with their services and data through your programs. They usually have extensive documentation on how to do this. In Github's [API documentation](https://developer.github.com/) you will see how to send requests to Github, what you will get in reply and how you can filter the reply for your specific needs. How about you check it out? Open up a new tab in the browser and go to the address _https://api.github.com/users/username_ with your own username in place of "username". You will see something similar to this:

{% highlight json %}
{
    "login": "gvanrossum",
    "id": 2894642,
    "avatar_url": "https://avatars.githubusercontent.com/u/2894642?v=3",
    "gravatar_id": "",
    "url": "https://api.github.com/users/gvanrossum",
    "html_url": "https://github.com/gvanrossum",
    "type": "User",
    "site_admin": false,
    "name": "Guido van Rossum",
    "company": "Dropbox",
    "blog": "http://python.org/~guido/",
    "location": "San Francisco Bay Area",
    "email": "guido@python.org",
    "hireable": null,
    "bio": null,
    "public_repos": 4,
    "public_gists": 5,
    "followers": 911,
    "following": 0,
    "created_at": "2012-11-26T18:46:40Z",
    "updated_at": "2015-12-23T18:35:37Z"
}
{% endhighlight %}

This is what I see when I visit the address with Python's daddy Guido's username: [https://api.github.com/users/gvanrossum](https://api.github.com/users/gvanrossum). I left out a few lines so that it's easier to read. You will notice that we can know many things about Guido's Github account: when it was created, how many public repositories he has, even how many people he is following. This is how it works: Github takes a few parameters from you (eg. Guido's username) and looks up the related information in their database. Depending on your access permission, Github then shows you a webpage with the available info. The page is in [JSON](www.json.org) format, that is, list of data in key-value pairs. Not very unlike Python's dictionaries.

## Let's start scraping!

__Who and what to scrape__ <br>
Now that we know what sort of data we can get, let's start coding. First of all we need to import the modules we are going to use in this project.
We will also be defining a few variables:

{% highlight python %}
import requests

def main():
    users = ['google','facebook','apache']
    baseurl = "https://api.github.com/users/"
    properties = ['login', 'id', 'html_url', 'public_repos', 'created_at']
{% endhighlight %}

You probably don't have `requests` module installed, so type `pip install requests` in your terminal for that. We can put any Github username in the `users` list, but for now let's keep Google, Facebook and Apache in it. `baseurl` is the URL to access Github's API. We will add usernames at the end of this URL to access that user's info. Finally, the `properties` list has a few properties of the JSON file that we are going to use.

__Test run__<br>
We have three users in our `users` list, so we will have to loop through it. But at let's test with one user first. Add these lines to your code:

{% highlight python %}
# to grab and show info from Github
    # make API url with Github username
    current_page = baseurl + "facebook"  # "https://api.github.com/users/facebook"
    # take response from the url
    response = requests.get(current_page)
    # conversion to human readable format
    json = response.json()
    # print properties in json dictionary
    print(json["name"])
    print(json["id"])
    print(json["html_url"])


if __name__ == '__main__':
    main()
{% endhighlight %}

Now run the code. Did you see something like this?

    Facebook
    69631
    https://github.com/facebook
    147
    2009-04-02T03:35:22Z

Now time for line by line explanation. After putting Facebook's API URL in `current_page`, we grab the page by this:

{% highlight python %}
    response = requests.get(current_page)
{% endhighlight %}

`response` object now holds the exact response that Github's server replied us with when we try to access the address in `current_page`. It has got the JSON information of Facebook, along with some other HTTP metadata. We take the JSON info from that using `json()` method and convert it into Python's familiar dictionary:

{% highlight python %}
    page_info = response.json()
{% endhighlight %}

We have all the necessary info in `json`, you can try testing that by printing it. But we only need the name, id and profile address this time, so:

{% highlight python %}
    # print properties in json dictionary
    print(page_info["name"])
    print(page_info["id"])
    print(page_info["html_url"])
{% endhighlight %}

__Looping through__<br>
We already completed our main objective, that is, scrap info from APIs. Congratulations to you! But we are not done yet. This is not an efficient way to get information of multiple users. So we need to loop through the `users` list as well as the `properties` list to get all the user's info. Let's rewrite our test code with the following loop like this:

{% highlight python %}
# to grab and show info from Github
    for username in users:
        # make API url with Github username
        current_page = baseurl + username
        # take response from the url
        response = requests.get(current_page)
        # conversion to human readable format
        page_info = response.json()
        for property in properties:
            print("{} : {}".format(property, page_info[property]))
        print('')
{% endhighlight %}

This time when we run the code, it will grab info of all the usernames in `users` list. Then print the properties of them too. **We can move on to [next stage]({{ site.url }}/scraping-github-3):** saving the info in to RethinkDB's database. Here is the full code for further use.

<script src="https://gist.github.com/mnzr/30fbd4e6fd3177a53f83.js"></script>
