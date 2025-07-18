---
layout: post
title: Facebook Enforces HTTPS - Developer's Thoughts
excerpt: "\"We've enforced HTTPS for all new API applications. Why? Just because we can.\" &copy; Facebook"
last_modified_at: 2019-01-28 23:00:18
categories: blog
tags: [facebook, https, development]
image:
  feature: facebook-https.png
---

> This blog post is a cry from the heart. I don't know why, but there are more and more people who don't even try to think before doing anything.

Some time ago Facebook introduced new requirements for API applications. They want you to use only HTTPS as a transfer protocol to protect sensitive data from insecure transfer. I'm happy about it, but as usual they half-assed it.

Most of the applications I have developed are deployed to AWS or similar providers and use the following infrastructure scheme:

[![Application Infrastructure](/images/application-architecture.png)](/images/application-architecture.png)

So, I handle the secure connection with a load balancer and the application doesn't even know about the existence of the HTTPS connection. It just works.
But locally developers don't have a load balancer and it's okay to communicate with the application over HTTP.

Facebook enforces HTTPS for applications and doesn't allow it to be turned off even if the application isn't published for users. Okay, there is another option: Facebook gives the ability to make a kind of 'test' version of an existing application with different API keys and secrets. But even in a test application you are still forced to use HTTPS. So you can't check Facebook login without HTTPS enabled. What the heck are you doing, Facebook?

I know that I can make a self-signed certificate or use a free certificate from [Let's Encrypt](https://letsencrypt.org/), but why should I mess with it if I can resolve it on the infrastructure layer? With this enforcement developers need to "implement" a local "secure" environment. Seriously?

<h3 style="text-align:center">🔥 Facebook burn in Hell! 🔥</h3>

**UPD January 24, 2019:** It is possible to use HTTP protocol for applications in development mode, but only with `localhost` domain.
