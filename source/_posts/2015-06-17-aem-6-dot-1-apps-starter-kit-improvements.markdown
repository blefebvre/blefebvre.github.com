---
layout: post
title: "AEM 6.1 Apps Starter Kit improvements"
date: 2015-06-17 22:16
comments: true
categories: [AEM, CQ, Apps, PhoneGap, Starter Kit, AEM Apps, mobile]
published: false
---
With [AEM 6.1 out the door](https://docs.adobe.com/docs/en/aem/6-1/release-notes.html), we've pushed some commits to the [Starter Kit repository](https://github.com/Adobe-Marketing-Cloud-Apps/aem-phonegap-starter-kit) to ensure that the apps you bootstrap with it are up to date with the present state of the art. What follows is a visual tour of a few of those improvements.

### Native Page Transitions

{% img /images/starter-kit-improvements/transitions.gif 'App showcasing native transitions' %}

Transitions between pages play a big role in how your app's performance is perceived yet perfecting this animation with CSS can stump even the sharpest front-end dev. The Starter Kit now comes with the excellent [Native Page Transition](https://github.com/Telerik-Verified-Plugins/NativePageTransitions) plugin enabled by default, delivery buttery smooth transitions while offloading the tedious `translate3d` (or was it `transform`?) animation to native hardware acceleration. Many thanks go to [@eddyverbruggen](https://twitter.com/eddyverbruggen) for all the hard work that went into this plugin!

<!-- more -->

### PhoneGap/Cordova CLI 5

{% img /images/starter-kit-improvements/phonegap_cli_5.gif 'PhoneGap CLI v5 in action' %}

The latest PhoneGap CLI release contains a [bunch of improvements](http://phonegap.com/blog/2015/04/28/phonegap-cli-5.0.0-0.27.0/), but is (unfortunately) not compatible with the version of Geometrixx that shipped with 6.1. Not to fear: we will be releasing a rev of the sample shortly. In the meantime our Starter Kit is all set to take advantage of the new CLI, including:
- Cordova Android@4, which introduces support for [Crosswalk](https://crosswalk-project.org/)!
- The use of config.xml to manage platforms and plugins - a tidy alternative to writing custom hooks

