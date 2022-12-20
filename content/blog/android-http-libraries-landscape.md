---
title: "Android HTTP Libraries: Exploring the landscape"
date: 2019-01-10T14:00:00Z
draft: false
tags: [http, android, software, libs, dev]
cover:
  image: /img/androidhttplibs.png
---

> Post originaly done on [Codavel's blog](https://blog.codavel.com/android-http-libraries-landscape)

Every day, we rely more and more on our smartphone to do a huge variety of our daily life tasks. We have on average [80 apps installed](https://www.appannie.com/en/insights/market-data/apps-used-2017/) on our smartphones and most of them rely on web services to be useful to the end-user. Typically, such apps make use of HTTP requests to fetch and push data to the web services.

While developing Bolina, I had to make some research around this, so I decided to share what I've learned. As an overview, this post discusses the current landscape of HTTP libraries for the Android environment: I look into the most used HTTP libraries, to their intended use case and how they compare to each other, in general. If you're looking for iOS HTTP Libs, follow this link. 

Even though I focus on HTTP libraries for the Android system, most of the discussed libraries are Java-based, which means they can also be used in any Java application. Despite such, I am not going to dive into details on the particularities of each library that refers to the general Java environment.

## Apache HTTP Client

[Apache HTTP Client](https://stuff.mit.edu/afs/sipb/project/android/docs/reference/org/apache/http/impl/client/DefaultHttpClient.html) was one of the first HTTP libraries included since Android’s API 1 (alongside HttpURLConnection). As its name implies, it was developed by the same guys that brought us the Apache web server - which, from my point of view, inspires confidence from the start. Thus, it became the default answer for many developers (myself included) in the early days of Android development.

However, the actual implementation of the library in Android was more of a [work performed by Google](http://hc.apache.org/httpcomponents-client-4.3.x/android-port.html) and lacked many updates and bug fixing implemented by the Apache team. Therefore, with [API 23 release](https://developer.android.com/about/versions/marshmallow/android-6.0-changes#behavior-apache-http-client) (Android 6.0), Google decided to deprecate the default Android’s Apache HTTP Client APIs.

### Pros

* Simple API
* Supports Android API 1+
* The only solution for API 8-
* Actual not must… Just don’t use it!

### Cons

* Old base code
* Not properly maintained
* Contains lots of bugs
* Again, don’t use it!

Notice that the guys from Apache.org also provides a full up-to-update port of the original Apache HTTP Client for Android. However, the usage of this port may cause some headache due to the conflict with the library and the native Apache HTTP Client implementation.

## HttpURLConnection

Similar to Apache HTTP Client, HttpURLConnection also appeared with Android API 1. However, it only became stable enough starting from API 9, when it became the [recommend HTTP library to be used by Google](https://android-developers.googleblog.com/2011/09/androids-http-clients.html). The benefit of being the default choice for many years is the huge amount of tutorials that can be found.

### Pros

* Complete control of HTTP connections
* Supports Android API 1+
* Updated frequently
* Automatic Gzip compression
* Support cached HTTP responses

### Cons

* Complex API
* Input and output streams are not buffered by default
* Lacks support for HTTP/2
* No advanced features
* Asynchronous calls must be orchestrated by the developer, e.g. via AsyncTasks

## Volley

As an effort to improve Android’s HTTP capabilities and in order to simplify the life of developers, in 2013 Google released the [Volley HTTP library](https://developer.android.com/training/volley/). This library provides methods to handle typical apps web requests, such as calls to RESTful APIs and image downloads. It also simplifies the management of the HTTP lifecycle by providing asynchronous calls, transparent HTTP caching, automatic scheduling of network requests and request prioritization. Also, Volley allows the developer to chose which HTTP he wants to use.

By default, Volley relies on Apache HTTP Client for older versions of Android and on HttpURLConnection for newer ones. However, it can also be configured to use OkHttp and make use of some of its features.

### Pros

* Simple API
* Asynchronous calls
* Transparent HTTP caching
* Automatic scheduling of network requests
* Request prioritization
* Helpers for RESTful APIs, images and raw text

### Cons

* Not suited for large files transfers
* Synchronous calls not easily implemented
* HTTP/2 support depends on the underlying HTTP engine used

## OkHttp

[OkHttp](https://square.github.io/okhttp/) is Square's answer for their HTTP needs, by being "[an HTTP client that’s efficient by default](http://square.github.io/okhttp/". It becomes efficient by automatically caching HTTP response, automatically compression HTTP data and performs connection pooling. Besides being efficient, OkHttp also has robustness as a goal: it employs a few strategies to automatically recover in case of a connection fail. Moreover, it has developed to support HTTP/2 from the beginning.

As if all these features were not enough, its APIs are simple and very well documented. It is so popular that Google, on Android 4.4, decided to [use OkHttp as the HTTP engine of HttpURLConnection](https://twitter.com/jakewharton/status/482563299511250944?lang=en).

### Pros

* Simple API
* HTTP/2 support
* Fully customizable
* Both synchronous or asynchronous calls
* Transparent HTTP caching
* Automatic Gzip compression
* Very good and complete documentation
* Updated frequently

### Cons

* Supports Android API 9+
* Lack of helpers for RESTful APIs and image downloads
 
## Retrofit

One of the characteristics of OkHttp, is its lack of helper for RESTful APIs and image downloads. I am calling it a characteristic and not a problem because that’s a design choice. Square created OkHttp as a very capable HTTP engine that can also be easily used by other libraries, in this case, [Retrofit](https://square.github.io/retrofit/). Retrofit, as with OkHttp, is also developed by Square. It turns a RESTful API into a Java interface, by using annotations to describe the HTTP request. Retrofit is also very customizable, allowing the developer to set various converters, to handle for e.g. XML requests.

### Pros

* Handles RESTful APIs
* OkHTTP under the hood

### Cons

* Only handles RESTful APIs
* Supports Android API 9+
 
## Picasso

Besides Retrofit, Square also developed another helper: [Picasso](https://square.github.io/picasso/). This time, to help with image loading directly into ImageViews. As with Retrofit, Picasso relies on OkHttp to perform its HTTP requests and to handle all network related challenges. Regarding image management, Picasso handles the ImageView recycling cycles, the complexity of image transformations with minimal memory use and finally manages memory and disk caching automatically.

### Pros

* Handles image loading
* Handles ImageViews recycling cycles
* Handles image transformations
* Automatic image caching
* OkHTTP under the hood

### Cons

* Only handles image loading
* Supports Android API 14+

These are the most used and acclaimed HTTP libraries for the Android universe. Each one of them has their strengths and weaknesses. However, in my opinion, there’s one library clearly surpasses the others: OkHttp. The guys from Square did a great job with their HTTP library, making it simple, fully customizable, full of features and very well documented. Plus, it is the base for many other amazing libraries.
