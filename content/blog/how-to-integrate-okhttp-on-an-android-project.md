---
title: "OkHttp: Android Tutorial"
date: 2019-01-24T14:00:00Z
draft: false
tags: [http, android, software, libs, dev]
cover:
  image: /img/okhttp1.png
---

> Post originaly done on [Codavel's blog](https://blog.codavel.com/how-to-integrate-okhttp-on-an-android-project)

Most of internet content distribution it is still done through HTTP, which means that anyone building a mobile app will be obviously dealing with HTTP Libs. Since we are working to improve the performance of content distribution on mobile apps, there was no doubt that our Bolina SDK would have to be seamlessly integrated with, at least, the most popular HTTP Libs out there. That was how I decided to start exploring the HTTP Libs landscape.

During this process, I figured out that, features-wise, OkHTTP is my favorite. [OkHTTP](https://square.github.io/okhttp/) is Square's answer for HTTP needs, by being "[an HTTP client that's efficient by default](http://square.github.io/okhttp/). It becomes efficient by automatically caching HTTP response, automatically compressing HTTP data and performing connection pooling. Besides this, OkHTTP also has robustness as a goal, by employing a few strategies to automatically recover in case of a connection fail. Moreover, it has been developed to support HTTP/2 from the beginning.

In this tutorial I will show you how to integrate OkHTTP into an Android project (literally a clean, straight-out-of-the-Android-Studio new project wizard). It will create 2 HTTP calls, a GET and a POST to a publicly available RESTful API, hosted by [reqres.in](https://reqres.in/), a service "that allows us to test our front-end against a real API".

First, add the permission to the application’s manifest to allow the application to access the Internet:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

To integrate the OkHTTP library into a project, we must include the OkHTTP dependency to the project’s gradle:

```java
implementation 'com.squareup.okhttp3:okhttp:3.12.0'
```

OkHTTP library exposes both synchronous and asynchronous methods to perform HTTP calls. I really like asynchronous methods, given that it removes the pain of managing the worker thread ourselves.

To perform a GET call, just add the following code to your project:

```java
OkHttpClient client = new OkHttpClient();

Request get = new Request.Builder()
        .url("https://reqres.in/api/users?page=2")
        .build();

client.newCall(get).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
        e.printStackTrace();
    }

    @Override
    public void onResponse(Call call, Response response) {
        try {
            ResponseBody responseBody = response.body();
            if (!response.isSuccessful()) {
                throw new IOException("Unexpected code " + response);
            }

            Log.i("data", responseBody.string());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
});
```

I start by creating a new _OkHTTPClient_ instance and by creating a new request. Finally, I just ask OkHTTP to perform the get request and to asynchronously notify when the response from the server is successful. That’s it, very simple!

To perform a POST call, just add the following code to your project:

```java
OkHttpClient client = new OkHttpClient();

JsonObject postData = new JsonObject();
postData.addProperty("name", "morpheus");
postData.addProperty("job", "leader");

final MediaType JSON = MediaType.parse("application/json; charset=utf-8");
RequestBody postBody = RequestBody.create(JSON, postData.toString());
Request post = new Request.Builder()
        .url("https://reqres.in/api/users")
        .post(postBody)
        .build();

client.newCall(post).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
        e.printStackTrace();
    }

    @Override
    public void onResponse(Call call, Response response) {
        try {
            ResponseBody responseBody = response.body();
            if (!response.isSuccessful()) {
                throw new IOException("Unexpected code " + response);
            }

            Log.i("data", responseBody.string());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
});
```

I start by creating a new _OkHTTPClient_ instance and by creating a JSON with data to post to the server.

Notice that in order to create JSON objects, include the GSON dependency to the project:

```java
implementation 'com.google.code.gson:gson:2.8.5'
```

After creating the JSON data, I create the post request and set it to send JSON data. Finally, I just ask OkHTTP to perform the post request and to asynchronously notify when the response from the server is successful. Again, simple!

OkHTTP also allows HTTP calls to be run synchronously. To use those, instead of using the method enqueue, just use the method execute.

## Full Project Code

The full code used is available on [Github](https://github.com/dferreira-cvl/howto-okhttp-android). Feel free to adapt it to your specific needs. I hope this tutorial helped you understanding how to integrate OKHttp into an Android project. If this wasn’t the right solution for you, you can also check the integration tutorial for HttpURLConnection, which is a more mature HTTP library. To help you decide, you can also check my performance comparison post, where I reveal which one is faster.
