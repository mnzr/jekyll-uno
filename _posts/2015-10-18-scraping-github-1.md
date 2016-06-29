---
layout: post
title: "Scraping Github - The Setup (Part 1)"
permalink: scraping-github-1
date: 2015-10-18
author: Ratul Minhaz
summary: Learn to scrape data from API using Python
categories: Learning
tags:
  - scraping
  - python
  - API
published: true
---

Data science is a buzzword these days and everyone is asking how to be a data scientist. A data scientist's job is definitely awesome, because if anyone is close to predicting the future, they are the ones who probably do it. But what does a data scientist actually do? The process of "data science" roughly consists of three things: mining, analysis and visualization. Each one of these three segments is interesting in it's own right. In data mining you get to collect data from large data sources, mostly from the Internet using clever techniques like scraping.
<!--more-->

> This is the first of a three part tutorial series on getting started with scraping data from Github:
>
> 1. [The Setup]({{ site.baseurl}}/scraping-github-1) (<-- you are here)
> 2. [Fetching Data]({{ site.baseurl}}/scraping-github-2)
> 3. [Storing to RethinkDB]({{ site.baseurl}}/scraping-github-3)
>

Many websites provide `API` so that other people can collect data from those sites to use in their work. For example you might want to know a Facebook page's growth over a certain period of time. Facebook made an API just so that you can get the data to do the analysis needed to answer your question. This sort of API is provided by Github and Twitter too. You can not imagine how vastly useful this public data is, they even came in handy as much as [detecting earthquakes](https://blog.twitter.com/2015/usgs-twitter-data-earthquake-detection) faster than the U.S. Geological Survey!

![earthquake_technology.png]({{site.baseurl}}/assets/images/earthquake_technology.png)

Enough talk, let's have some action! In this tutorial, I will get you started with scraping data from API using Python. We will use Github's API as data source and keep the scraped data in a database called RethinkDB. Don't worry if you have never heard of it before, it's pretty cool. If you want to try out other databases, you will just have to tweak a few lines of code. Let's set up the necessary softwares.

# Setup
You will need to install a few softwares, preferably in a Linux workstation. I am going to use Ubuntu. If you are using Windows, then switch to Ubuntu or any other *nix. There is no way around it. You will need-

- Python 3
- [Virtualenv and virtualenvwrapper](http://docs.python-guide.org/en/latest/dev/virtualenvs/)
- RethinkDB 2.0.x
- Sublime Text 3 or any good text editor for coding

__Virtualenv and virtualenvwrapper__ <br>
You probably have already used these if you tried to solve version dependency in your code. These are handy tools that create Python virtual environments that are separate and isolated from eachother. Follow this [link](http://docs.python-guide.org/en/latest/dev/virtualenvs/) to read more and install them. Create and activate a virtual environment for this project:

{% highlight bash %}
mkvirtualenv scraping
workon scraping
{% endhighlight %}

__RethingDB__ <br>
If you are new to Ubuntu or Linux, installing RethinkDB might be a little bit complicated for you. Nothing you can't do though, just a few lines of extra commands to copy and paste. RethinkDB has really nice docs starting with [installation](https://www.rethinkdb.com/docs/install/) and trying it for the [first time](https://www.rethinkdb.com/docs/quickstart/).

_TIP: RethinkDB server won't start automatically everytime you log on to your PC, so you might want to set it up for [autostart](https://www.rethinkdb.com/docs/start-on-startup/)._

You will also need the Python driver to connect to the database using your code. Start up the virtual environment you created earlier and install the driver:

{% highlight bash %}
pip install rethinkdb
{% endhighlight %}


__Now you are worthy enough to move on to [next stage]({{ site.baseurl}}/scraping-github-2) :D__

<a href="http://www.twitter.com/share?text={{ page.title}}&url={{ page.url | prepend: site.baseurl | prepend:site.url }}&via=mnz_r">
                    Tweet</a> to me if you have any questions or suggestions.
