# Cross-Language Benchmarking Framework - POC Project
## Introduction
In 2010 I graduated in media computer science in the field computer graphics or, in more detail 'photo'realistic image generation using MonteCarlo path tracing methods. For more details, please have a look at my thesis [here](https://ben-steinert.net). At that time, I implemented a MonteCarlo PathTracer named "Chroma" in C++.
In 2015 I did a restart of my renderer, but I wanted to take up the challenge with Java, because this is the eco system I work with today in my daily job. And I wanted to see, whether the following statement from 2010 is still valid:

> C/C++ will outperform Java in the field of computer graphics.

The following downsides are more or less obvious:

- Garbage collection can cause a lot of pauses and arbitrary memory fragmentation
- Objects/Values cannot be structured in such a detail as C/C++ allows it (structs, pointers value types)
- Lack of control over intrinsics (heavy SIMD operations which unleash the true power of todays CPUs)

Now the motivation is still to get as close as possible. But how would you actually 'measure' it? How can you say whether an alternate implementation in Java performs well? 

## Benchmarking, yes :-)
Benchmarking is a good indication to decide whether the performance of an implementation of an algorithm is 'good'. Roughly, 'good' is usually only verifiable when figures like throughput changes, ideally increases. But how to compare implementations in two different lagnuages??


## Cross-language benachmarking, oh yes :-D

Cross-language benchmarking is common in the typical language wars. Someone presents figures for language A, someone else for language B. Given that the execution conditions (there are quite a lot unfortunately) are close to identical, an indication might be concluded.


## CroLaBeFra
The goal of CroLaBeFra is exactly that. To be precise, the following requirements drove me the last 6 months while working on this topic:
1. Gradle and Gradle Plugins shall be the basis of CroLaBeFra
2. Java shall be benchmarked with [JMH](http://openjdk.java.net/projects/code-tools/jmh/)
3. C++ code shall be benchmarked with [Hayai](https://github.com/nickbruun/hayai)
4. Results shall be transformed in one common format and shown in one comparison dashboard.
5. The results shall be historized


## Results
The result of the last six months are the following three Gradle plugins which take care of 1. - 4. (The historization topic is currently in 'evaluation'):

- [crolabefra-java-plugin](https://github.com/comsysto/crolabefra-java) ( id: com.comsysto.gradle.crolabefra.java )
    - Wraps the jmh-gradle-plugin to provide convenient access to a jmh execution task
    - Converts the Hayai benchmarking results to a CroLaBeFra format
- [crolabefra-cpp-plugin](https://github.com/comsysto/crolabefra-cpp) ( id: com.comsysto.gradle.crolabefra.cpp )
    - Downloads Hayai from a forked version of Hayai, add the library to the project and assembles it together with your cpp code
    - Converts the Hayai benchmarking results to a CroLaBeFra format
- [crolabefra-mothership-plugin](https://github.com/comsysto/crolabefra-mothership) ( id: com.comsysto.gradle.crolabefra.mothership )
    - Looks for CroLaBeFra enhanced modules in the project and executes the according tasks
    - Collects all results
  
### Side benefits

- The cpp and java plugin are independent of each other. This means, you can use the framework also in a single language mode. Even the mothership plugin can be skipped. Then, only the result collection and dashboard creation gets lost.  
- You can use the cpp-plugin and have a convenient way to integrate Hayai into your Gradle C/C++ project. 

## This POC project
Now, enough text, clone this repository and play around with it! Be careful, there are a LOT of submodules! One idea was to keep the production code and the benchmarks in separate projects.
The init shorthand which should initialize and checkout the right things for you:

    git submodule update --init --recursive
    
The only command you need, in order to

- run the contained JMH benchmarks
- download Hayai, compile link and run the benchmarks
- convert the results
- create a dashboard 

is:

    gradle crolabefra

In

    ./build/reports/mothership/index.html

you will find the CroLaBeFra dashboard, which might look more or less like that:

![CroLaBeFra Dashboard Screen](https://raw.githubusercontent.com/bensteinert/crolabefra-setup-poc/master/dashboard.png)


And if you wanna see, what all the rendering code is actually doing, run the JavaFxMain in the chromarenderer-java submodule and hit "Enter" when the white screen appears.
Currently, both rendering projects are under heavy construction. Check the different branches if you like. But no guarantee that they work ;)

## Current caveats

- In order get somehow comparable numbers, I had to bend the JMH settings a bit, in order to match the way Hayai executes the benchmarks. I also explicitly wanted to have the code warmup to be part of the benchmarks, because it more precisely represents what happens, runtime-optimization. So please don' punish me for my benchmarking options. These should be defined by the plugin in the near future.
- For Hayai, I had to implement a custom json exporter. Meanwhile, the author Nick Bruun also provided a json exporer. So I should switch to the main repository again. Currently, the plugin fetches a forked version for which I take care.
- The matching of the according benchmarks is currently done by method name. This is ok for the first prototype. But for more complex projects, it should probably be configurable from the outside (the mothership root project)
- The creation of the dashboard feels a bit clumsy. I have to improve here ;)
- When cleaning the project, an internet connection is mandatory, in order to fetch the Hayai lib from github
- In the reports, no details about the host hardware, used compilers and runtimes are given.
- No Windows support ( this might not change ;) )

This is really just a very first drop of my approach! I hope to find the time to improve a lot in the next months!

For more background information about the project, I refer to my blog posts:

* Part 1 - [_Cross-language benchmarking made easy?_](http://blog.comsysto.com/2015/02/20/cross-language-benchmarking-made-easy/)
* Part 2 - [_Cross-language benchmarking – Gradle loves native binaries!_](http://blog.comsysto.com/2015/02/13/cross-language-benchmarking-gradle-loves-native-binaries/)
* Part 3 - [_Cross-language benchmarking - Git submodules and the single-command cross language benchmark_](http://blog.comsysto.com/2015/06/15/cross-language-benchmarking-part-3-git-submodules-and-the-single-command-cross-language-benchmark/)

Please send me your feedback!
So long!
Cheers 
Ben

