---
title: "OkHttp vs HttpURLConnection Performance: Comparing Android HTTP Libraries"
date: 2019-01-17T14:00:00Z
draft: false
tags: [http, android, software, libs, dev, performance]
cover:
  image: /img/androidhttplibs.png
---

> Post originaly done on [Codavel's blog](https://blog.codavel.com/android-http-libraries-performance-comparison-okhttp-vs.-httpurlconnection)

When designing a mobile app we need to start considering performance since day one, and choosing the best HTTP Lib is just one of the first steps… and if you’re performance-freaks as I am, you also understand that the performance of transfer speeds each HTTP Library is capable of achieving is more or as important as its features. It doesn’t make sense to prioritize simplicity in application maintenance over application performance, especially when we know that [delays in serving content cost you money](https://blog.codavel.com/mobile-app-performance-1-sec-delay).

In this first comparison, I start by evaluating the transfer speeds OkHttp achieves and compare them with the results of Android's default library, HttpURLConnection. Take a look.
 
## Test environment

For the intended comparison, I used two setups:

* Android emulator, with a Nexus 5X API 28 x86 image, running on a 13-inch MacBook Pro 2017, with an i5 2.3GHz CPU and 8GB of RAM
* OnePlus 5, with Android 9.0

Both the OnePlus 5 and MacBook Pro are associated to a 5GHz Wi-Fi network, connected to the Internet via a 1Gbps connection.

## Let’s measure it

To measure the performance of each HTTP library I created a [simple test application](https://github.com/dferreira-cvl/okhttp-vs-httpurlconnection-android) that, after app launch, perform a download of a small file (1.1MB) or a big file (47MB) from an Ookla’s speedtest.net server. For that purpose, and given that I am located in Portugal, I selected a server also located in Portugal.

The following tables present the achieved transfer times. Notice that the presented results correspond to the average of 30 measurement samples and all times are in milliseconds.

#### Table 1 - Small File (times in milliseconds)

|  | **OkHttp** | **HttpURLConnection** |
|:---:|:---:|:---:|
| **Emulator** | 406 | 405 |
| **OnePlus** | 116 | 111 |
 
Table 1 refers to the transfer times of a small 1.1MB file. Results show that OkHttp achieves identical performance when compared with HttpURLConnection on the emulator.

#### Table 2 - Big File (times in milliseconds)

|  | **OkHttp** | **HttpURLConnection** |
|:---:|:---:|:---:|
| **Emulator** | 7002 | 7510 |
| **OnePlus** | 1594 | 1680 |

Table 2 refers to the transfer times of a big 47MB file. Results show that OkHttp achieves slightly better performance when compared with HttpURLConnection for transfers of larger files, by transferring the same file almost 100ms faster on a smartphone and 500ms faster in the emulator.

The results are somewhat expected given that both libraries actually depend on the performance of TCP, the underlying protocol of HTTP/1.1 and HTTP/2. Nevertheless, OkHttp is capable of achieving a small performance boost for large files.

The full code used for the comparison is available in [Github](https://github.com/dferreira-cvl/okhttp-vs-httpurlconnection-android), feel free to adapt it to your specific needs.

## Performance at the core of user-experience

After this experience, it is easy to understand that OkHttp is capable of providing a small performance boost when compared with HttpURLConnection. In my quest to understand the landscape of Android HTTP libraries, I admitted that, feature-wise, OkHttp was my favourite. After this results, there are just no reasons not to choose OkHttp for my next Android project. If you agree, here’s a tutorial on how to easily integrate it. If it was the mature HttpURLConnection that caught your way, there’s also a tutorial for you. 

However, your performance concerns shouldn’t end here. With performance at the core of the user-experience, it is critically important to think on how can you get the best performance possible at all development stages. When it comes to mobile apps, this is even more important. Even though the world is going mobile, **the core of content distribution** (HTTP, which uses TCP for data transport) was designed for a wired world, and **is not prepared to deal with the inherent instability of wireless links**.

Luckily, there are already a few solutions on-the-making.