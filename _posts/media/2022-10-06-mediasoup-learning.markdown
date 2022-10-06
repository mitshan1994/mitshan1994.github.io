---
layout: post
title:  "Mediasoup Learning Notes"
categories: media
---

[A simple mediasoup demo](https://github.com/mkhahani/mediasoup-sample-app) is used in learning.

### Steps of Producing Media
For a **mediasoup** based server application, media should first be **produced**, which means inputing media data into mediasoup. Now, we assumes there is only one router. The following are the basic steps:
1. Client connects to mediasoup server. This step isn't included in the mediasoup part, but in the signaling system.
2. Client gets **rtp capabilities** from server, and do some check on it.
3. Client creates a **device** (such as **mediasoup.Device**), using *rtp capabilities*. It represents an endpoint connecting to a mediasoup router.
4. Client signals to server, requesting it to create a server-side webrtc transport, which is used to receive media data later. And server passes created transport info back to client.
5. Client uses server-side webrtc transport info to create a **send transport**, to send media to server later.
6. Provide *client send transport* info to server-side transport.
7. Client sends video/audio track info to server (using **transport.produce**).
8. Server instructs the router to receive audio/video data.

### Steps of Consuming Media
1. Client connects to mediasoup server, to get existing routers and other information.
2. Client creates a **device** to represent the local endpoint.
3. Client signals to server, requesting it to create a server-side webrtc transport, used to send media data later. And server passes the created transport info back to client.
4. Client creates **receive transport** using remote endpoint transport data, and starts consuming.
5. Client signals to server when **receive transport** is ready. Server inputs the client-side transport info into server-side transport.
6. Server receives **consume** signal and creates a consumer, with specific producer.
