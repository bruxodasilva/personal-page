---
title: "Why and How to adopt a MultiCDN Approach"
date: 2022-02-28T14:00:00Z
draft: false
tags: [cdn, multicdn, infrastructure, performance, ux, protocols, network]
cover:
  image: /img/Codavel.webp
---

> Post originaly done on [Codavel's blog](https://blog.codavel.com/why-and-how-to-adopt-a-multicdn-approach)

**TL;DR:** With a MultiCDN approach, we can improve app performance and user experience, while increasing the app resilience and optimizing your costs.

## The CDN Approach Challenge

Suppose you are a Solution Architecture, a Product Manager, a VP of Engineering, or a CTO. In that case, you already know CDNs play a vital role in serving traffic across the globe by caching and serving content much closer to end-users. CDNs benefit your app by improving your user experience. All these benefits while providing high availability and reducing demand on your origin servers. And all at a fair cost. And as our appetite for better content grows, so has our dependency on CDNs.

![](/img/Map-CDN.png#center)

However, a problem arises. Not all CDNs were created equal. All CDNs offer the same base functionality, such as caching content at the edge. However, CDNs are not equally strong in all areas. Some give superb control over the caching engine. Others prefer to focus on vast PoP diversification, while some integrate easily with cloud features and ecosystems. Others focus on solving the challenges posed by serving data to mobile devices. Finally, some extend their feature set to support edge computing.

Now, you have a decision to make. Which one should you choose? The right CDN to your app should increase performance and reliability and match your feature set needs. Moreover, relying on a single CDN exposes you to huge risks, such as the risk of degraded performance, slowdowns and outages. Even the best CDNs suffer from hardware issues. Even though a single CDN may be able to provide a satisfactory or even a good service most of the time, they will never be capable of delivering an always perfect service, with exceptional performance for all regions.

I have good news for you. You do not need to stick to a single CDN to service all your needs!

## The Multi-CDN Approach

So, when you cannot select one, select all of them!

![](/img/ppost-CDN.png#center)

Joke aside, you should develop your app and service to use multiple CDNs, in a multi-CDN approach. With it, your content is hosted with more than one CDN provider, enabling you to improve your app performance further. Also, in a multi-CDN environment, you far exceed simple vendor diversity. The advantages are performance gains, [unlocking new markets](https://blog.codavel.com/how-to-unlock-new-markets-using-a-mobile-cdn), higher user experience, and cost savings. Let’s see how to achieve it.

## How to do it

The mechanisms of selecting CDNs and forwarding traffic in a multi-CDN strategy can be achieved in many ways. Let's dissect multiple approaches to achieve such a strategy. Notice that, independent of the technique implemented, selecting a CDN should be based on several criteria: availability, capacity, geographic coverage, performance, traffic type, cost, or combinations of the above.

### Static DNS

Each distinct hostname is pointed to a unique CDN. For example, you can point image.example.com to a CDN with specialized image compression or streaming.example.com to a CDN with a great video streaming engine, while all other traffic is forward to a CDN with the cheapest pricing per traffic volume.

### Round Robin DNS

Each domain can point to multiple CDNs, and each DNS reply will be served in a round-robin strategy. Thus, traffic will be balanced among the intended CDNs. Moreover, a weighted round-robin mechanism can also be deployed when a CDN is preferred over another. Finally, this strategy can be augmented by including health checks to the CDNs endpoints at the DNS level, which will cause traffic to be forward to CDNs that are not currently experiencing an outage, for example.

### User Geolocation DNS

As stated, you may want to leverage distinct CDNs for distinct regions. This can be achieved by performing [geolocation-based DNS resolution](https://constellix.com/products/geodns), where DNS service answers users' queries considering the user's location. For example, with this strategy, users in India can be pointed to CDN X, while users in China can be pointed to CDN Y.

### Client-side Decisions

Even though DNS-based solutions can be easily implemented, monitored, and deployed, they do not consider the user experience and how the multi-CDN decision affects such user experience. One way to overcome such challenges is to rely on client-side performance monitoring, such as the one provided by APMs. This strategy offers a much more unambiguous indication of the user's experience. It allows you to make decisions at a global level, region level, or even individual level. This data can be combined with the DNS-based strategies to provide a more informed picture.

Moreover, client-side decisions can be used to augment existing DNS-based mechanisms. Still, they can also be used standalone if the aggregate clients' data is exposed via an SDK, for example.

## The Benefits

As previously introduced, relying on a multi-CDN strategy, although more complex than implementing a single CDN approach, brings many potential benefits. Let’s unveil some of the major benefits of having a multi-CDN implementation in place.

### Extended Geographic Coverage

Every CDN has its distinct geographic coverage, and no CDN is alike. The most popular ones offer similar geographic coverage around the most popular regions, such as Europe or the United States. However, the same is not valid for some other regions. Some CDNs provide better coverage in Asia, others in South America, others in Africa. With a multi-CDN, you can leverage the specific CDNs for specific regions.

### Performance

Even when multiple CDNs offer the same or similar geographic coverage, their performance may differ among those regions. CDNs operate their PoPs differently in unique data centers with distinct network peering contracts. Thus, you can benefit by comparing these CDNs against each other and diverting traffic to the CDN or PoP that currently provides the best performance.

### Availability and Redundancy

CDN services operate on vast infrastructure and networks. However, they are not unlimited. Significant peaks in traffic may overwhelm even the best and most performant CDN. On top of that, CDNs may also experience global or localized outages. In case of such events, you can divert traffic from one CDN to another with a multi-CDN approach. Thus, you are minimizing single points of failure.

### Specialized Features

Some CDNs provide specialized features, such as image optimizers, static content optimization, live video streaming engines, or advanced security mechanisms. For example, Codavel’s CDN offers a [unique value proposition](https://www.bruxodasilva.xyz/blog/why-did-we-build-a-mobile-cdn/) for mobile apps, given that it is specifically designed to improve the user experience, even in the most challenging network environments. Some CDNs integrate very well into existing cloud environments. By leveraging a multi-CDN strategy, you can use individual CDNs for each of the distinct features they are best known for.

### Optimized Costs

Most CDNs provide unique pricing models. Some charge based on regions or volume of traffic, while others charge based on peak and off-peak hours, to name some examples. By using a multi-CDN approach, you can leverage the most cost-effective CDNs for your use case and divert traffic to the CDN that offers the best price for a given user, region, or even a given time.

## Assurance and Best Practices

When deploying a multi-CDN strategy, problems will for sure arise. Or you may need to guarantee that all decisions are being made properly. Here you can find some tips on how to improve your chance to overcome the challenges and help you achieve a successful multi-CDN deployment.

### Monitoring Parameters and KPIs

By leveraging a multi-CDN strategy, you can improve your app's performance. However, it would help to assess that properly if you had the necessary visibility. Consistent monitoring parameters across the multiple CDNs make it easier to compare the performance of each CDN, which will allow a more granular fine-tune for your multi-CDN strategy.

### Employ Third-party Monitoring

Real-time monitoring is of great importance for a multi-CDN deployment. However, not all CDNs offer real-time traffic monitoring or even unified monitoring. To better achieve your multi-CDN monitoring, we recommend using APMs or RUM solutions, which will allow you to compare multiple CDNs at an equal level. Failing to attain consistent monitoring may lead to conflicting results and bad decisions.

### Consistent Headers

To help you in the diagnostic process, you should ensure consistent headers across all CDN providers. Problems that you may need to address. What nodes or regions served the request? What is the request ID? Was it a cache hit or miss? How long did it take for the CDN to populate the cache? Consistent headers will help you with these questions. If your CDNs do not provide consistent headers, you can always process them in the client or leverage the power of edge computing to achieve such an end.

### Multi-DNS

A single DNS provider can be a single point of failure in your multi-CDN strategy. As with any approach, you should rely on a highly available and redundant DNS strategy by deploying two DNS providers, which will ensure resilience against the failure of a DNS provider.

### A/B Testing

To further improve upon your multi-CDN deployment, you should also implement a robust A/B testing strategy. When used in conjunction with the collected monitoring data, this testing strategy allows you to benefit from further optimizations by quickly testing the impact of new approaches.

## Conclusion

Delivering exceptional user experiences to your app is more important than ever. In most cases, depending on your content delivery to a single CDN does not provide the level of resiliency or performance your app requires.

A correctly implemented multi-CDN strategy will help you provide the top-notch app your users expect. Unfortunately, implementing such a multi-CDN strategy is not as simple as selecting a set of CDNs and turning them on. It requires understanding your app, your content, and your clients. A correct multi-CDN strategy will improve your app user experience, increase its redundancy and availability, and has a huge potential to optimize your costs.
