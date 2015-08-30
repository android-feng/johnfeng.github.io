---
layout: post
title: "okHttp updates: POST wouldn't be allowed to have null body"
date: 2015-06-30 22:42:48 +0800
comments: true
share: true
categories: anroid okhttp post
---
## Problem

The [OkHttp](https://github.com/square/okhttp) lib has been updated to 2.4.0 in late May , it has been a long time since last version 2.4.0 RC released in mid March with a lot of [changes](https://github.com/square/okhttp/blob/master/CHANGELOG.md). And one thing affects my projects most is the POST requests' limitation.

<!--more-->

According to the [ChangeLog](https://github.com/square/okhttp/blob/master/CHANGELOG.md):

> *  **`Request.Builder` no longer accepts null if a request body is required.**
    Passing null will now fail for request methods that require a body. Instead
    use an empty body such as this one:
    ```
        RequestBody.create(null, new byte[0]);
    ```

And the real changes in code is : [page](https://github.com/square/okhttp/blob/master/okhttp/src/main/java/com/squareup/okhttp/Request.java#L248)
{% codeblock lang:java Builder updates %}
    public Builder method(String method, RequestBody body) {
      if (method == null || method.length() == 0) {
        throw new IllegalArgumentException("method == null || method.length() == 0");
      }
      if (body != null && !HttpMethod.permitsRequestBody(method)) {
        throw new IllegalArgumentException("method " + method + " must not have a request body.");
      }
      if (body == null && HttpMethod.requiresRequestBody(method)) {
        throw new IllegalArgumentException("method " + method + " must have a request body.");
      }
      this.method = method;
      this.body = body;
      return this;
    }
{% endcodeblock %}

Since in our projects, lots of POST request are created with null body , eg : **likeObject** requests and **commentObject** requests. Usually we add these parameters , without any Personally identifiable information, directly append the request url , instead of into the Map entities in request forms , where they should be. And from 2.4.0, these will lead to compile error says ***"POST methods must have a request body"*** or get NPE ***"invoke length method on a null object"***


##Fix
Since we don't time to fix every single API with null body , we build a method to avoid this error. Inside of request's init class, we override the **getBody()** method of the BaseAPIRequest, which extends ApiGsonRequest< T> class created from Volley and Gson libs, to return a Byte[0] when request get null body. It looks different from official suggestion, but write once , fix all . 

> *Just inside the smallest project we have , over 40 API requests have been created, that's why we really don't have time to fix them one by one or add a meaningless parameter to each null POST request to avoid this error.*

the final code should be like :

{% codeblock lang:java Override getBody %}
    @Override
    public byte[] getBody() throws AuthFailureError {
        byte[] body = super.getBody();
        if (null == body) {
            body = new byte[0];
        }
        return body;
    }
{% endcodeblock %}





