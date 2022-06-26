---
title: "All You Need to Know About New Hermes Javascript Engine How to Migrate"
date: 2019-07-13T20:04:45+05:30
draft: true
---

> SO FACEBOOK JUST OPEN SOURCE ANOTHER JAVASCRIPT ENGINE NAMED AS HERMES.  
> IT IS A JAVASCRIPT ENGINE SPECIFICALLY TAILORED FOR RUNNING WITH MOBILE APPLICATIONS THAT IS PRIMARILY WITH REACT NATIVE ON ANDROID BECAUSE ON IOS YOU ARE FORCED TO USE V8.

![](https://web.archive.org/web/20200814101001im_/https://i1.wp.com/code.fb.com/wp-content/uploads/2019/07/HermesOSSChainReact_blog_FIN_1-1.gif?w=696&ssl=1)

By Marc Horowitz

Mobile applications are growing larger and more complex. Larger apps using JavaScript frameworks often experience performance issues as developers add features and complexity. These issues are generated from various spots, but the people using these apps expect them to run smoothly, regardless of the device they are on.

According To Facebook:

” To increase the performance of Facebook’s apps, we have teams that continuously improve our JavaScript code and platforms. As we analyzed performance data, we noticed that the JavaScript engine itself was a significant factor in startup performance and download size. With this data in hand, we knew we had to optimize JavaScript performance in the more constrained environments of a mobile phone compared with a desktop or laptop. After exploring other options, we built a new JavaScript engine we call Hermes. It is designed to improve app performance, focusing on our React Native apps, even on mass-market devices with limited memory, slow storage, and reduced computing power. “

## How Hermes improves React Native performance

For JavaScript-based mobile applications, user experience benefits from attention to a few primary metrics:

-   The time it takes for the app to become usable, called time to interact (TTI)
-   The download size (on Android, APK size)
-   Memory utilization

![Metrics for MatterMost React Native app running on a Google Pixel, similar in performance to popular phones in markets like India.](https://web.archive.org/web/20200814101001im_/https://i1.wp.com/code.fb.com/wp-content/uploads/2019/07/hermesstats-1.jpg?w=696&ssl=1)

Metrics for MatterMost React Native app running on a Google Pixel, similar in performance to popular phones in markets like India.

Notably, our primary metrics are relatively insensitive to the engine’s CPU usage when executing JavaScript code. Focusing on these metrics leads to strategies and trade-offs that differ from most existing JavaScript engines today. Consequently, our team designed and built Hermes from scratch. As a result of this focus, our implementation provides substantial improvement for React Native applications.

Because Hermes is optimized for mobile apps, we do not have plans to integrate it with any browsers or with server infrastructure such as Node.js. Existing JavaScript engines remain preferable in those environments.

## Key Hermes architectural decisions

Mobile device limitations, such as smaller amounts of RAM and slower flash, led us to make certain architectural decisions. To optimize for this environment, we implemented the following:

### Bytecode precompilation

Commonly, a JavaScript engine will parse the JavaScript source after it is loaded, generating bytecode. This step delays the start of JavaScript execution. To skip this step, Hermes uses an ahead-of-time compiler, which runs as part of the mobile application build process. As a result, more time can be spent optimizing the bytecode, so the bytecode is smaller and more efficient. Whole-program optimizations can be performed, such as function deduplication and string table packing.

The bytecode is designed so that at runtime, it can be mapped into memory and interpreted without needing to eagerly read the entire file. Flash memory I/O adds significant latency on many medium and low-end mobile devices, so loading bytecode from flash only when needed and optimizing bytecode for size leads to significant TTI improvements. In addition, because the memory is mapped read-only and backed by a file, mobile operating systems that don’t swap, such as Android, can still evict these pages under memory pressure. This reduces out-of-memory process kills on memory constrained devices.

![Hermes: An open source JavaScript engine optimized for mobile apps, starting with React Native](https://web.archive.org/web/20200814101001im_/https://i1.wp.com/code.fb.com/wp-content/uploads/2019/07/HermesOSSChainReact_blog_FIN_1-1.gif?w=696&ssl=1)

Although compressed bytecode is a bit larger than compressed JavaScript source code, because Hermes’s native code size is smaller, Hermes decreases overall application size for Android React Native apps.

### No JIT

To speed execution, most widely used JavaScript engines can lazily compile frequently interpreted code to machine code. This work is performed by a just-in-time (JIT) compiler.

Hermes today has no JIT compiler. This means that Hermes underperforms some benchmarks, especially those that depend on CPU performance. This was an intentional choice: These benchmarks are generally not representative of mobile application workloads. We have done some experimentation with JITs, but we believe that it would be quite challenging to achieve beneficial speed improvements without regressing our primary metrics. Because JITs must warm up when an application starts, they have trouble improving TTI and may even hurt TTI. Also, a JIT adds to native code size and memory consumption, which negatively affects our primary metrics. A JIT is likely to hurt the metrics we care about most, so we chose not to implement a JIT. Instead, we focused on interpreter performance as the right trade-off for Hermes.

### Garbage collector strategy

On mobile devices, efficient use of memory is especially important. Lower-end devices have limited memory, OS swapping does not generally exist, and operating systems aggressively kill applications that use too much memory. When apps are killed, slow restarts are required and background functionality suffers. In early testing, we learned that virtual address (VA) space, especially contiguous VA space, can be a limited resource in large applications on 32-bit devices even with lazy allocation of physical pages.

To minimize memory and VA space used by the engine, we have built a garbage collector with the following features:

-   On-demand allocation: Allocates VA space in chunks only as needed.
-   Noncontiguous: VA space need not be in a single memory range, which avoids resource limits on 32-bit devices.
-   Moving: Being able to move objects means memory can be defragmented and chunks that are no longer needed are returned to the operating system.
-   Generational: Not scanning the entire JavaScript heap every GC reduces GC times.

**How To Migrate and Start using Hermes Engine:**

First, ensure you’re using at least version 0.60.2 of React Native. If you’re upgrading an existing app ensure everything works before trying to switch to Hermes.

Edit your  `android/app/build.gradle`  file and make the change illustrated below:

```
  project.ext.react = [
      entryFile: "index.js",
-     enableHermes: false  // clean and rebuild if changing
+     enableHermes: true  // clean and rebuild if changing
  ]
```

Next, if you’ve already built your app at least once, clean the build:

```
$ cd android && ./gradlew clean

```

That’s it! You should now be able to develop and deploy your app as normal:

```
$ react-native run-android

```

## Confirming Hermes is in use

If you’ve just created a new app from scratch you should see if Hermes is enabled in the welcome view:

![Where to find JS engine status in AwesomeProject](https://web.archive.org/web/20200814101001im_/https://i2.wp.com/facebook.github.io/react-native/docs/assets/HermesApp.jpg?w=696&ssl=1)

A  `HermesInternal`  global variable will be available in JavaScript that can be used to verify that Hermes is in use:

```
const isHermes = () => global.HermesInternal != null;

```

To see the benefits of Hermes, try making a release build/deployment of your app to compare. For example:

```
$ react-native run-android --variant release

```

This will compile JavaScript to bytecode during build time which will improve your app’s startup speed on device.

At the end, I will Show you a comparision between Hermes Engine & V8

> This article was recovered from my old blog salmanually.com and reposted here on (26/06/2022)
