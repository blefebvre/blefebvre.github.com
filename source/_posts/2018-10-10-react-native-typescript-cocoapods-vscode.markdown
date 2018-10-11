---
layout: post
title: "Getting started with React Native, TypeScript, CocoaPods, and VSCode"
date: 2018-10-10 12:28
comments: true
categories: [React Native, SQLite]
published: false
---
This post gives an introduction to my preferred development setup of 2018 for building iOS applications: React Native with TypeScript and CocoaPods, with the awesome Visual Studio Code editor.

Over the past 8 months I've had the pleasure of working on a side project with a very special customer: my Dad. His idea was to create an iOS app to give users a consolidated view of the investments they hold across multiple accounts. I suggested that such a thing must surely already exist -- which it did, but none of the existing offerings suited him, primarily due to his comfort level in trusting the app developer to store and manage his financial data in a secure manner. With the constant stream of news covering large Fortune 500 corporations being hacked, I couldn't blame him for this concern. With the delivery of the app's requirements in paper form accompanied by a hand-written cover letter, the project was on.

<!-- 
The solution we came up with was one where the data would be stored locally on-device, which we found to have a number of benefits:

- There would be no server for us to manage, patch, keep online, and serve as a single point of failure for the app
- There would be no server-side code to develop, debug, load test, and monitor
- The app would work offline out-of-the-box, since this would be the primary use case
- If our users' wished to sync their data with another device, the app could be integrated with a service like Dropbox (a pattern we'd seen work well in other apps, such as [1Password](https://1password.com/))
-->

<!-- We were sold on the approach, and I began looking into options for storing relational data device-side with minimal overhead. SQLite quickly became the natural choice: it's fast, rock solid, and has been battle tested for years accross a huge array of platforms and devices. -->

<!-- more -->

In an effort to use this side project as a learning opportunity, I picked a technology that I'd been interested in for ages but had yet to find a use case for: React Native. As a huge React fan for building web UIs I was instantly hooked. There were a few gotchas along the way but my experiene was almost entirely positive. I intend to cover a few interesting aspects of building this app over the next few blog posts (including SQLite, Dropbox integration, syncing data between devices, and testing!) but this one with focus on simply setting up the React Native project that I'll build upon in future posts. 


## Getting started

I followed a blog post on Facebook's React Native blog to [bootstrap my React Native app](https://facebook.github.io/react-native/blog/2018/05/07/using-typescript-with-react-native) (abbreviated to RN going forward), along with TypeScript support -- a typing tool I no longer intend to develop JavaScript without. I'll glaze over many of the details here, but the article is excellent if you wish to get a bit more detail on the steps that I followed. Please feel free to skip this section if you already have a working React Native app.

Initialize the RN app using the [RN CLI](https://facebook.github.io/react-native/docs/getting-started.html#the-react-native-cli) (replace `RNSQLiteDemo` with a string of your choice, but note that it is used later on as well):

    react-native init RNSQLiteDemo

At the time of writing, I was using Node `v8.12.0` and react-native `2.0.1`.


## TypeScript

Next, add TypeScript and the handy rimraf tool as dev dependencies, and initialize TypeScript with `tsc`:

    npm install --save-dev typescript react-native-typescript-transformer rimraf
    npx tsc --init --pretty --jsx react

Note use of npx above, as opposed to npm. This will use the locally installed version of TypeScript (in `node_modules/`), and save you having to install a global version of the library.

Next, rename `App.js` to `App.tsx` (an extension which means "this is a TypeScript file which may also contain JSX").

Open the directory containing your app with the excellent [Visual Studio Code](https://code.visualstudio.com/) editor. Open up the recently renamed App.tsx file. Right away you will notice that it has detected some issues:

{% img /images/react-native/0-ts-errors-in-vscode.png 'Code underlined in VSCode indicating errors that were found' %}

We need the TypeScipt [type definition](http://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html) packages for React and React Native:

    npm install --save-dev @types/react @types/react-native

All that red underlining should now be resolved. (Is it not? Check out the [Facebook article](https://facebook.github.io/react-native/blog/2018/05/07/using-typescript-with-react-native) to debug further)

We need a special config file to support RN with TypeScript: rn-cli.config.js. Create it now:

    touch rn-cli.config.js

And add the following contents to it:

    module.exports = {
        getTransformModulePath() {
            return require.resolve('react-native-typescript-transformer');
        },
        getSourceExts() {
            return ['ts', 'tsx'];
        },
    };

In the tsconfig.json file (generated earlier by the `npx tsc ...` command), uncomment the `// "lib"` line, and add the `es2015` string to the array:

    ...
    "lib": ["es2015"],
    ...

Further down in tsconfig.json, uncomment the following line:

    "allowSyntheticDefaultImports": true,

Lastly in the TypeScript department, we need to set up an npm script to transpile our TypeScript code into regular JS that the RN bundler can consume. I recommend adding the following script to `package.json`:

    ...
    "scripts": {
        <existing scripts...>,
        "tsc": "tsc",
        "clean": "rimraf build",
        "build": "npm run clean && npm run tsc --",
        "watch": "npm run build -- -w"
    }
    ...

Now you can watch your source for TypeScript errors and have it transpiled into JS on each save with the following command (try it now!):

    npm run watch


## CocoaPods

I want to make this as real-world an example as possible, so we're going to be using CocoaPods as well. [This article on shift.infinite.red](https://shift.infinite.red/beginner-s-guide-to-using-cocoapods-with-react-native-46cb4d372995) has a large amount of detail on the CocoaPods setup, but I am going to skip right to the commands I used to initialize my project.

If you do not yet have CocoaPods installed:

    gem install cocoapods

Init CocoaPods in the ios/ directory:

    cd ios/
    pod init

Open up your Podfile with an editor of your choice. I like VS code for it's excellent TypeScript support, but any editor will work:

    code Podfile

Replace the contents of your Podfile with the following (assumes you've named your app ``; simply replace that string if that's not the case):
```
target 'RNSQLiteDemo' do
  # Pods for RNSQLiteDemo

  pod 'yoga', :path => '../node_modules/react-native/ReactCommon/yoga'

  pod 'React', :path => '../node_modules/react-native', :subspecs => [
    'DevSupport',
    'RCTLinkingIOS',
    'RCTImage'
  ]
  
  # Uncomment the next line (remove "#") once you have installed the react-native-sqlite-storage package from npm
  # pod 'react-native-sqlite-storage', :path => '../node_modules/react-native-sqlite-storage'

end

post_install do |installer|
  installer.pods_project.targets.each do |target|
    # The following is needed to ensure the "archive" step works in XCode.
    # It removes React & Yoga from the Pods project, as it is already included in the main project.
    # Without this, you'd see errors when you archive like:
    # "Multiple commands produce ... libReact.a"
    # "Multiple commands produce ... libyoga.a"

    targets_to_ignore = %w(React yoga)
    
    if targets_to_ignore.include? target.name
      target.remove_from_project
    end
  end
end

```

Tell CocoaPods to process your Podfile and install the Pods you've included above (still in the `ios/` dir):

    pod install

If successful, you should see a message printed to the console that indicates something to the effect of:

> Pod installation complete! There are 4 dependencies from the Podfile and 2 total pods installed.

If you see a failure at this point, I would recommend digging in to the [shift.infinite.red article](https://shift.infinite.red/beginner-s-guide-to-using-cocoapods-with-react-native-46cb4d372995) and resolving the issue before moving on.

At the time of writing, [facebook/react-native/issues/21310](https://github.com/facebook/react-native/issues/21310) was open which meant that I had to add `@babel/runtime` via npm to enable the Metro bundler to correctly create an app bundle:

    npm add @babel/runtime
    npm install

This is a good point to check that the app is in fact runnable. To avoid overcomplicating this tutorial I am going to focus on iOS only, but each dependency I have included will run on Android as well. Per the `pod install` console output, make sure to use the `RNSQLiteDemo.xcworkspace` file to open your iOS project from now on (still in `ios/` dir):

    open RNSQLiteDemo.xcworkspace

In Xcode, select a simulator to target and hit the play button to build and run your app on the simulator.

