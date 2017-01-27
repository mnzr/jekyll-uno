---
published: false
---
## Setting

Trying to set up SASS with Ember can be quiet problematic if you are using a version manager for Node. For example I was using a version manager called 'n' and could not integrate SASS in anyway with my Ember app. It all boiled down to my Ember installation not finding libraries needed to compile SASS. These two modules were the reason of the problem: node-gyp and node-sass.

After fiddling around for a while I found out the proper way to setup SASS with Ember:

'''sh
n latest						# switch to the latest version of Node
npm install -g node-gyp
npm install -g node-sass
ember install ember-cli-sass	# this will install Ember addons for SASS
'''