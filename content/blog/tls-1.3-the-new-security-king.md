---
title: "TLS 1.3: The New Security King"
date: 2018-11-05T14:00:00Z
draft: false
tags: [security, networks, protocols, tls]
cover:
  image: /img/tls1.3.png
---

> Post originaly done on [Codavel's blog](https://blog.codavel.com/tls-1.3-the-new-security-king)

I am pretty sure we have all heard of Transport Layer Security (TLS) at some point. TLS protocol provides security for HTTP (yes, it is the S in HTTPS!). It guarantees a secure communication pipe between a client (being it your browser or app) and a server where - and by a secure communication pipe, I mean all data in the pipe is encrypted. Every time we access Gmail, access our bank account or check our friends’ Instagram photos, we are using the security provided by TLS.

TLS 1.3 brings major benefits and some vulnerabilities that every developer or project manager should be aware of - and that I’m about to explore. Before getting there, let’s just recall how TLS became so ubiquitous.

## The history of TLS

We can trace back TLS’s begins to mid-nineties and to a protocol named Secure Sockets Layer (SSL). Developed by Netscape, SSL versions 1 (never released due to major security flaws), 2 and 3 set the foundation for the requirements of TLS, by exposing the challenges and by breaking the first stones into a completely secure solution.

In the late nineties, Netscape and Microsoft started a big war: both companies were implementing unique security solutions targeted exclusively to their homemade browsers. In the middle of it, Netscape understood that the effort to improve SSL security and to increase its compatibility was through standardization. To achieve that vision, Netscape handed SSL standardization efforts to IETF.

Under IETF, and with heavy influence from SSL 3, a new standard was born: TLS 1.0. Even though very similar to SSL 3, TLS 1.0 was not interchangeable with the latter.

> _Fun fact: TLS 1.0 was to be called SSL 3.1. However, Microsoft was not happy with that. The company didn’t want TLS to be seen as an evolution of SSL and not of its own secure protocol (named PCT - Private Communication Technology) - even though it totally was! Also interesting was the fact that Microsoft started working on PCT based on the efforts on SSL 2… The guys from Redmond knew how to play it fair and square!_

By the mid-2000s, IETF introduced TLS 1.1 to the world, which added some defenses against vulnerabilities exposed by TLS 1.0. A couple of years later, TLS 1.2 was introduced. Besides adding support for new cryptographic algorithms, this new version introduced new ways to encrypt data and verify the data’s integrity via authenticated encryption with associated data (AEAD) mechanisms. This allowed eliminating some flaws present on previous protocols.

By this time, TLS was a well-established protocol and almost every service accepts to use it, given its benefits over its cost, mainly processing cost. At the same time, Google started with the mission of increasing the overall performance of Internet communications.

Within its efforts, Google developed [SPDY](https://www.chromium.org/spdy/spdy-whitepaper) and [QUIC](https://www.chromium.org/quic) protocols. These new protocols started to experiment faster ways to provide the same security features of TLS 1.2, such as reducing handshake times and improving the processing required by the cryptographic algorithms.

Based on Google’s knowledge and others, IETF started developing a new security protocol, TLS 1.3. This new protocol was set to:

- Improve protocol’s security
- Improve protocol’s performance

At the same time, IETF set to remove legacy features that have proved to pose serious security flaws.

## A new security protocol has arrived: welcome TLS 1.3

A new protocol was finally unveiled (at least on its final version) on August 2018. This new protocol sets apart from the old ones, by rethinking all process to achieve a secure communication pipe.

### State-of-the-art ciphers and key exchange

TLS 1.3 deploys many lessons learned from previous protocols. One example of such lessons learned was that previous protocols did not mandate the use of key exchange algorithms that provided forward secrecy. Forward secrecy guarantees that no one can decrypt a prior secure communication pipe, even if the attacker is capable of accessing the current server’s private key. Therefore, TLS 1.3 mandates the use of ephemeral [Diffie-Hellman](http://mathworld.wolfram.com/Diffie-HellmanProtocol.html) as the only key exchange mechanism, ensuring that only algorithms that provide forward secrecy are used.

Over the years, many security vulnerabilities have been discovered, especially in the way that ciphers were used. For example, CBC-mode block ciphers and RC4 stream cipher were exploited on many occasions, allowing attackers to completely break the encryption mechanism and gain access to the original data. TLS 1.3 takes drastic measures against this and similar cipher that pose issues in the past, with CBC-mode ciphers and RC4 cipher no longer available. In fact, TLS 1.3 goes even further, allowing only AEAD ciphers to be used, which merge the process of encrypting and integrity verification. All ciphers and mechanisms that were previously considered vulnerable were removed from TLS 1.3.

### Improved handshake

The major efforts on TLS 1.3 to improve [its performance](/blog/measuring-tls-1.3-performance/) were made on the time spent on the handshake. Previous versions of TLS require 2-RTT for a handshake to occur. Based on Google’s efforts on QUIC, TLS 1.3 reduces his handshake to just 1-RTT. To achieve this, the client sends negotiation information in the hello messages, with the server doing the same thing.

Moreover, TLS 1.3 also allows a client to reconnect to a known server in 0-RTT, by sharing a resumption master secret, meaning that both peers do not waste any time negotiating a secure pipe prior to sending useful data.

![](/img/tls-1.png#center)

Contrary to older versions, with TLS 1.3 all algorithms negotiation occur in an independent manner. This is, the key exchange algorithm can be negotiated independently from the cipher used for the communication pipe. In another version of TLS, these options were bundled together.

Finally, on previous versions of TLS, the handshake was only partially signed, making them vulnerable to downgrade attacks. The logic behind a downgrade attack is to make the peers of the pipe agree on a cipher that is prone to man-in-the-middle attacks, such as CBC-mode ciphers. To address those flaws, on TLS 1.3 the handshake is fully signed.

Nevertheless, do not forget that TLS 1.3 is not capable of achieving true 0-RTT connection establishment. TLS 1.3 still requires TCP to go through its 3-way handshake. In fact, only a [UDP based protocol such as Google’s QUIC](https://blog.codavel.com/quic-vs-tcptls-and-why-quic-is-not-the-next-big-thing), is capable of true 0-RTT.

### New features, new vulnerabilities

Even though TLS 1.3 brings many improvements to the table, it also creates new challenges and exposes new pandora boxes that may be used for potential attacks. One of these potential attacks can come when a client is allowed to connect to a known server using a 0-RTT feature.

Given that the client and the server do not establish a complete handshake, the shared resumption master secret does not benefit from forwarding secrecy. As we previously discussed, this would allow a potential attacker to decode an encrypted pipe if he manages to access the resumption master secret.

![](/img/gcp-cloud-cdn-performance.gif#center)

Moreover, using the 0-RTT feature to communicate with a known server, it opens the door for replay attacks. In the 0-RTT connection establishment, the server simply accepts the data from the client if the resumption master secret is correct. If we consider that the client’s data is an order to transfer money to a bank account, an attacker could simply repeat the message sent by the client to server and the server would count the transaction twice. Not very good for the client…

Therefore, to mitigate this kind of attacks, it is recommended caution when using the 0-RTT connection establishment. As an example, the application should perform only GET requests to the server with the 0-RTT connection establishment, requiring connection awareness on the application.

### A new protocol, slow deployments

We shouldn’t also forget that TLS 1.3 usability is limited by many factors, such as web browsers, web server, CDN providers, among others. For example, at the time I am writing this post, on the web servers side, only Apache, NGINX, HAProxy, and H2O (thankfully the big ones) support TLS 1.3, with only HAProxy and H2O supporting TLS 1.3 0-RTT connection establishment. No node.js or ISS here.

And things do not look brighter when we look into CDN providers. At the moment, only CDN77, Cloudflare and KeyCDN support TLS 1.3. No support from AWS CloudFront, Akamai nor Google Cloud CDN (interesting, considering Google was one of the companies that led the efforts on defining TLS 1.3 features). Looking into browsers, only Firefox and Chrome support TLS 1.3.

We still have a long way to go and let's hope this time around companies do not take a long time to add support for TLS 1.3. However, looking into the past, Apple and Microsoft took 5 years to support TLS 1.2 on their browsers...

### Long live the king!

TLS 1.3 is the next step into a better and more secure Internet. It is securer, faster and simpler than ever. Let’s just hope companies understand the importance of security and make an effort so that TLS 1.3 becomes the default secure protocol used for every Internet traffic.

Even though security must be a top priority, we must not forget the tradeoff between security and speed. TLS 1.3 does exactly that, by trading some security for the reduction of handshake time by allowing 0-RTT connection establishment.

I believe that we must always look for solutions that solve the security challenges we’re facing, while making sure that such solutions do not impair or reduce the performance of our services (performance matters!). I hope that this keeps being a concern on future developments that will for sure occur on future versions of TLS.

#### _Images sources_

* https://www.keycdn.com/blog/tls-1-3-support
* https://cloudplatform.googleblog.com/2018/06/Introducing-QUIC-support-for-HTTPS-load-balancing.html