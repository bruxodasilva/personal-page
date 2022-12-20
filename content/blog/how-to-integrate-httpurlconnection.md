---
title: "HttpURLConnection: Android Tutorial"
date: 2019-01-10T14:30:00Z
draft: false
tags: [http, android, software, libs, dev]
cover:
  image: /img/androidhttplibs.png
---

> Post originaly done on [Codavel's blog](https://blog.codavel.com/android-http-libraries-landscape)

Most of internet content distribution it is still done through HTTP, which means that anyone building a mobile app will be obviously dealing with HTTP Libs. Since we are working to improve the performance of content distribution on mobile apps, there was no doubt that our Bolina SDK would have to be seamlessly integrated with, at least, the most popular HTTP Libs out there. That was how I decided to start exploring the HTTP Libs landscape.

HttpURLConnection was one of the first libs I’ve decided to integrate, and it is easy to know why: ever since Android API 9, HttpURLConnection has become the recommended HTTP library for Android applications.  Here’s what I’ve learned, how to integrate it and some caveats that every developer should take into consideration.

I am integrating it into a clean, straight out of the Android Studio new project wizard. This tutorial will create 2 HTTP calls, a GET and a POST to a publicly available RESTful API, hosted by [reqres.in](https://reqres.in/), a service "that allows us to test our front-end against a real API".

## Integrating HttpURLConnection

Starting integrating HttpURLConnection is simple, given that the HTTP library is included in the Android API since API 1. However, we must take into consideration the fact that the library is not prepared for native asynchronous calls, requiring us to make use of workers, such as AsyncTasks, to perform HTTP network calls outside the main UI thread.

First, add the permission to the application’s manifest to allow the application to access the Internet:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Now let’s create an AsyncTask to perform an HTTP GET call:

```java
private static class HTTPReqTask extends AsyncTask<Void, Void, Void> {
    @Override
    protected Void doInBackground(Void... params) {
        HttpURLConnection urlConnection = null;

        try {
            URL url = new URL("https://reqres.in/api/users?page=2");
            urlConnection = (HttpURLConnection) url.openConnection();

            int code = urlConnection.getResponseCode();
            if (code !=  200) {
                throw new IOException("Invalid response from server: " + code);
            }

            BufferedReader rd = new BufferedReader(new InputStreamReader(
                    urlConnection.getInputStream()));
            String line;
            while ((line = rd.readLine()) != null) {
                Log.i("data", line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
        }

        return null;
    }
}
```

If you are familiar with Android development, this class is pretty straightforward. The main logic of the worker is located inside the _doInBackground_ method. First, I set the desired URL, followed by establishing an HTTP connection, via the _openConnection_ method. After checking if the connection was successful, I read the connection’s data from a _BufferedReader_, in this case, reading it, line by line. And that’s it!

To call this HTTP GET worker, just start the AsyncTask, for instance on the onCreate method of the main activity:

```java
new HTTPReqTask().execute();
```

To perform a POST call, just create the following AsyncTask:

```java
private static class HTTPReqTask extends AsyncTask<Void, Void, Void> {
    @Override
    protected Void doInBackground(Void... params) {
        HttpURLConnection urlConnection = null;

        try {
            JsonObject postData = new JsonObject();
            postData.addProperty("name", "morpheus");
            postData.addProperty("job", "leader");

            URL url = new URL("https://reqres.in/api/users");
            urlConnection = (HttpURLConnection) url.openConnection();
            urlConnection.setRequestProperty("Content-Type", "application/json");
            urlConnection.setRequestMethod("POST");
            urlConnection.setDoOutput(true);
            urlConnection.setDoInput(true);
            urlConnection.setChunkedStreamingMode(0);

            OutputStream out = new BufferedOutputStream(urlConnection.getOutputStream());
            BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(
                    out, "UTF-8"));
            writer.write(postData.toString());
            writer.flush();

            int code = urlConnection.getResponseCode();
            if (code !=  201) {
                throw new IOException("Invalid response from server: " + code);
            }

            BufferedReader rd = new BufferedReader(new InputStreamReader(
                    urlConnection.getInputStream()));
            String line;
            while ((line = rd.readLine()) != null) {
                Log.i("data", line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
        }

        return null;
    }
}
```

The first thing I do is create JSON data to post to the service. To create JSON data, we need to include the gradle dependency:

```java
implementation 'com.google.code.gson:gson:2.8.5'
```

Then create the URL of the post to be performed, followed by establishing the HTTP connection. Next, I set the HttpURLConnection options to perform the POST and write the JSON data into the connection. Finally, I just check if the post was successfully received by the server and check the server's reply. Again, simple!

As previously, to run this worker, just call it from anywhere in your project, via:

```java
new HTTPReqTask().execute();
```

## Full project code and final thoughts

The full code used is available [at Github](https://github.com/dferreira-cvl/howto-httpurlconnection-android). Feel free to adapt the code according to your specific needs. I hope this helped you integrating HttpURLConnection into an Android project, performing GET and POST calls, and understanding how to properly used it outside the main UI thread, via an AsyncTask.

In the end, HttpURLConnection is a mature HTTP library that can be integrated with relative ease, even though I have to admit that I prefer approaches like the more modern OkHttp library. Since it provides native asynchronous methods, it significantly simple to integrate it on a project. However, I can’t deny that performance is my main concern (and interest), so I had to find out which one was faster. Would you bet on HttpURLConnection or OkHttp? Check my results and find out!
