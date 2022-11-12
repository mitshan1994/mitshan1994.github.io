---
layout: post
title:  "HttpClient Notes"
categories: c#
---

### Customed HTTP Request
If some customed headers are required in request, we can use class `HttpRequestMessage` and `HttpClient.SendAsync()` to send a http request.

#### Add Authorization Header
We can set `HttpRequestMessage.Headers.Authorization` property to ours. For example:
```
var httpRequestMessage = new HttpRequestMessage();
httpRequestMessage.Method = HttpMethod.Get;
httpRequestMessage.RequestUri = new Uri("http://127.0.0.1/api");
httpRequestMessage.Headers.Authorization = new AuthenticationHeaderValue("Basic", "mytoken");
```

