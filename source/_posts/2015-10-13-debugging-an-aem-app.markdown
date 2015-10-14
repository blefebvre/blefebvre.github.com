---
layout: post
title: "Debugging an AEM App"
date: 2015-10-13 22:51
comments: true
categories: [AEM, CQ, Apps, PhoneGap, Starter Kit, AEM Apps, mobile]
---
Does your AEM App get stuck at a blank screen when you run it via the Cordova CLI?

{% img /images/debug-apps/app_init_fail.png 'Failure to initialize' %}

The following steps will help you get to the root of the error on both iOS and Android.

<!-- more -->


## iOS

These steps assume you have a functional Cordova build toolchain (including Xcode and the iOS Simulator), and are able to run `cordova platform add ios && cordova emulate ios` without any CLI related errors. 


#### Enable Develop menu in Safari

From Safari's menu, open "Preferences...", and navigate to the "Advanced" tab. 

Make sure the checkbox labelled "Show develop menu in menu bar" is checked:

{% img /images/debug-apps/dev.png 'Develop mode checkbox' %}


#### Connect the web inspector to your broken app

From the freshly enabled Develop menu, initiate a connection to your app running on the iOS Simulator (I'll be using the [kitchen-sink](https://github.com/blefebvre/aem-phonegap-kitchen-sink) app for this post):

{% img /images/debug-apps/develop_menu.png 'Develop mode checkbox' %}

You will be greeted by a new Web Inspector Safari window that is connected to the AEM App running on your iOS Simulator.


#### Refresh to see initialization errors

Navigate to the Console tab. You shouldn't see any errors, aside from perhaps Safari attempting to load source maps that have not been provided. In order to see any errors which occured during the initialization of the app, you will need to refresh the app. Located the refresh button and give it a click:

{% img /images/debug-apps/reload_app.png 'Reload app' %}

You should now see a few lines of debug logging, along with an error! 

{% img /images/debug-apps/console_output.png 'Console output' %}

In my case it appears that the ContentSync variable is missing. This variable is exported by the PhoneGap ContentSync plugin, so I would begin my bug investigation by seeing if this plugin has been successfully installed.

If this issue is occurring on an app obtained from an open source repo, the console output is extremely valuable and should be included in the bug report.


## Android


