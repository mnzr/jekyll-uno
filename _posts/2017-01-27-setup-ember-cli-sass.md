---
published: false
layout: post
permalink: datasci-intro-4-bn
date: 2017-01-27T00:00:00.000Z
author: Ratul Minhaz
summary: Setting up Ember CLI Sass properly
categories: Tips
tags:
  - Ember
  - Sass
---
## Setting

Trying to set up Sass with Ember can be quiet problematic if you are using a version manager for Node. For example I was using a version manager called 'n' and could not integrate Sass in anyway with my Ember app. It all boiled down to my Ember installation not finding libraries needed to compile Sass. These two modules were the reason of the problem: node-gyp and node-sass.

After fiddling around for a while I found out the proper way to setup Sass with Ember:

'''sh
n latest						# switch to the latest version of Node
npm install -g node-gyp
npm install -g node-sass
ember install ember-cli-sass	# this will install Ember addons for SASS
'''
