---
layout: post
title: "React Native with an SQLite database"
date: 2018-09-29 12:28
comments: true
categories: [React Native, SQLite]
published: false
---
Over the past 6 months I've had the pleasure of working on a side project with a very special customer: my Dad. His idea was to create an iOS app to give users a consolidated view of the investments they hold across multiple accounts. I suggested that such a thing must surely already exist -- which it did, but none of the existing offerings suited him, primarily due to his comfort level in trusting the app developer to store and manage his financial data in a secure manner. With the constant stream of news covering large Fortune 500 corporations being hacked, I couldn't blame him for this concern. 

The solution we came up with was one where the data would be stored locally on-device, which we found to have a number of benefits:

- There would be no server for us to manage, patch, keep online, and serve as a single point of failure for the app
- There would be no server-side code to develop, debug, load test, and monitor
- The app would work offline by design, out of the box
- If our users wished to sync their data with another device, the app could be integrated with a service like Dropbox (a pattern we'd seen work well in other apps, such as 1Password)

We were sold on the approach, and began looking into our options for storing relational data device-side with minimal overhead. SQLite quickly became the natural choice: it's fast, rock solid, and has been battle tested for years accross a huge array of platforms and devices.

<!-- more -->

I wanted to use this side project as a learning opportunity, so I picked a technology that I'd been interested in a while but had yet to find a project to experiement with it: React Native. As a huge React fan for building web UIs I was instantly a fan. There were a few gotchas along the way but my experiene was almost entirely positive. I plan to cover a few interesting aspects of this app that were particularly fun to work on over the next few blog posts, but this one with focus on using SQLite from within a React Native app for storing relational data. 

## Getting started

I followed a blog post on Facebook's React Native blog to bootstrap my React Native (abbreviated to RN going forward) app, along with TypeScript support -- a typing tool I no longer wish to develop JS without. I'll glaze over many of the details here, but the articles (TODO link to them) are excellent if you wish to get a bit more detail on the steps that I followed. Please feel free to skip this section if you already have a working React Native app.

Initialize the RN app using the RN CLI:

    react-native init SQLiteDemo

At the time of writing, I was using Node `v8.12.0` and react-native `2.0.1`.

(Optional, but recommended!) Next, add TypeScript, and initialize it:

    npm install --save-dev typescript react-native-typescript-transformer
    npx tsc --init --pretty --jsx react

Note use of npx above, as opposed to npm. This will use the locally installed version of TypeScript, and save you having to install a global version of the lib.



