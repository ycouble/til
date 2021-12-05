---
title: MPLS Basics
description: Traffic Engineering and route protection
date: February 5, 2015
categories: [networking]
image: images/blog/MPLS.png
---




 This article is an introduction to MPLS principles, and is intended as the first part of a wiki i'm making for my work.
 


MultiProtocol Label Switching (MPLS), as its name suggests, is based on label switching. The objective of this protocol is to reduce the processing time of a packet in a router by bypassing route tables as much as possible. MPLS messages are not switched relatively to their destination but according to the label the have been applied.

Basically, all packet are routed/classified according to their destination, type of service, customer and many other criteria when they enter the MPLS network. After that, the only thing that matters for MPLS devices is the labels assigned to the packet.  

Naturally, MPLS is a core network technology and is designed to be used for high volume transportation (when routing tables become too long and that traffic can be classified).

At MPLS network ingress a label is *pushed* inside the packet, then for the whole travel through MPLS network, labels are *swapped* at each node and eventually *popped* when egressing the MPLS network.


Label switching is pre-calculated and advertised through a signalling protocol. To ensure the availability of bandwidth and avoid congestions, ressources in the MPLS network are reserved by this protocols, creating pathes and protection pathes from one point to the other.


MPLS allows to classify traffic according to many criteria into several Forward Equivalent Class (FEC). Each class is supposed to include traffic that have the same kind of needs and expectations from the network. Classify traffic allows to share the ressources accordingly.

There are typically 5 very different types of classes:

* Network control messages: signalling, network time, etc. They must be forwarded whatever the conditions. e.g. NTP, IS-IS...
* Voice: low bandwidth, but with very restrictive requirements on latency and jitter. e.g. SIP, RTP...
* Live Streaming: high bandwidth, highly jitter-dependent and sometimes demanding low jitter. e.g. RTSP, RTP, Streaming protocols.
* Data that have to be carried in a reasonable time. e.g. http pages
* Low priority data: low requirement traffic. e.g. FTP...


MPLS allows to extend the range of a local network over IP networks. For example it is possible to have multiple geographically distant sites on the same local network, with the MPLS network being seen a a single switch. These VPNs are called epipes, Virtual Leased Lines(VLL) or Virtual Private LAN Service VPLS.

Also, it is possible to only hide the internet for a customer and to interconnect "directly" two distant sites with a virtual router carried over the whole MPLS network. These VPNs are called Virtual Private Routed Networks.


Most of MPLS signalisation protocols have been extended to a traffic engineering version with new algorithms for path calculation allowing to optimize pathes accross the MPLS network following several criteria.

As the signalling protocol has the vision of the whole network, it is able to organize paths and protection schemes. For example, it is possible to protect a specific path from a signle (or double, or p simultaneous) failure(s). Retrospectively, this is the kind of algorithms i was working on during my internship at GERAD.
