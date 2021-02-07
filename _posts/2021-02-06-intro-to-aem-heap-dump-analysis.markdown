---
layout: post
title: "An introduction to AEM heap dump analysis"
summary: "..."
twitter_image: "/images/aem/..."
date: 2021-02-06 06:14
comments: true
tags: [AEM, java]
published: true
---
There comes a point in every Adobe Experience Manager (AEM) developers career when you will need to analyze a heap dump. If you're anything like me, you'll open it up and have the following thought:

<img src="{{ site.baseurl }}/images/general/no-idea.png" alt="'I have no idea what Im doing' Sponge Bob screenshot" width="450" >

This post aims to give you a bit of experience by walking through the analysis of a couple of example heaps that we will generate ourselves.

## Tools of the trade

Before we begin you will need to have the following apps and tools on your machine:

- A recent version of AEM (6.5 preferably), publish instance, running locally. Do NOT use a shared dev/stage or (heaven forbid) prod instance for this!
- 👆 really! In order to generate our example heaps I'm going to provide some same code which has memory issues. It's best that this code stay far away from important environments.
- Apache JMeter for generating some load: [https://jmeter.apache.org](https://jmeter.apache.org)
- Eclipse Memory Analyzer tool (MAT) for analyzing the heaps: [https://www.eclipse.org/mat/](https://www.eclipse.org/mat/)

## Running AEM with a small memory allocation pool

In order to make it easier to encounter an OutOfMemory situation, I'm going to suggest _for this tutorial only_ that you start AEM with a small memory allocation pool. I've found `512mb` works, in that AEM will run on my Mac, and makes it fairly easy to exhaust that memory pool. 

Note, this is not a recommended setting for any sort of real environment! Just for learning about heap dumps, and only on your local AEM instance.

Use the following JVM args to start your AEM publish instance with a `512mb` memory allocation pool:

```
java -Xms512m -Xmx512m \
    -XX:+HeapDumpOnOutOfMemoryError \
    -jar aem-publish-p4503.jar -nointeractive -nofork
```

Note that it's best if you've run AEM once with more _typical_ `-Xms` and `-Xmx` values, to allow it to perform the more memory intensive initial installation process. When I tried "from scratch" (as in, no `crx-quickstart/` directory next to the quickstart .jar), I had my first heap dump collected while the WE.Retail site was loading. If this happens to you, try restarting AEM before you continue:

<img src="{{ site.baseurl }}/images/aem/heap-analysis/oom-during-startup.png" alt="" width="450" >
oom-during-startup.png

A keen eye will notice another important arg is included above: `-XX:+HeapDumpOnOutOfMemoryError`. This instructs the JVM to take a heap dump whenever an OutOfMemory error is encountered.



