---
title: DTN & Bundle Protocol
description: Services
date: February 24, 2015
categories: [satellite, networking]
image: images/blog/mail.jpg
layout: post
toc: false
comments: true
hide: false
search_exclude: false
---




This article follows the previous [introduction to DTN](http://couble.ovh/Delay-Tolerant-Network.html).

Satellite Networks differ from classical ground wired/wireless networks in many ways. Among which:

* Long to very long delays
* Error bursts on the signal channel, especially on the ground to space segment
* Unreliable topology: a node may become unreachable for a period of time without notice, or have a scheduled time span of availability
* Each node is really expensive to deploy, hence aborting or failing a packet transmission is very costly


Network utilization and its resiliency to error is a critical issue in satellite networks, and its optimization is a vast area of research.
  

The delay-tolerant or disruption tolerant network aims to unifying communications in a global overlay network, defining how actors may transfer data according to their needs.
Different services are defined, as well as their goals, in the DTN Architectures RFC and its direct protocol translation: the Bundle protocol.
  

Both RFC have been written by the same authors (NASA, Google, MITRE, Intel).

The Bundle protocol is intended as an overlay, transport independant protocol, proposing Quality of service, custody transfers with delivery options for unicast, anycast, multicast and broadcast communications.
  
Below is the ascii version of the Network stack.

[![The Bundle Protocol Network Stack, extracted from RFC 5050.](./DTN & Bundle Protocol - Notebook_files/Bundle_Protocol_Stack.png)](http://couble.ovh/figures/Bundle_Protocol_Stack.png)Figure 1: The Bundle Protocol Network Stack, extracted from RFC 5050. [[PNG](http://couble.ovh/figures/Bundle_Protocol_Stack.png)]

The Bundle protocol is closely similar in idea to the postal services. Actually, it is largely inspired from it.
For example:

* the postal services give the possibility to ask for an acknowledgement, which is shipped with the mail itself.
* Postal offices keep mails for a long period of time before a postman delivers it to destination.
* A very remote village with only one postman delivering mail once a week can easily be compared to a satellite being in line of sight of the closest node only one day a week.
* Likewise, the possibility to monitor the delivery status of one's package is often offered by mail services, with each post office reporting the reception/forwarding of the package.

The Bundle Protocol works exactly like that, and offers the same type of services to the applications using it.


The Bundle protocol defines two entities: the node and the endpoint:

* The node is the router running a bundle protocol instance
* Nodes can register to enpoints. An endpoint is the final destination node of a bundle.
 A node can participate in one or more endpoints but has to be part of an endpoint where he is the only one, called a singleton.
 A node is identified by an endpoint ID. The singleton endpoint ID of a node is then a unique identifier for this node.

Endpoint ID are similar to a postal address or a web URL. An URI scheme is defined for the DTN architectures (more generically than just for the Bundle Protocol).


### The Bundle Protocol Data Unit

The classical Bundle Protocol Data Unit (BPDU) is shown below.
This is only an example, as only a template is defined. The BPDU has customizable fields (the same way HTTP has).

[![Example of a Bundle Protocol Data Unit, extracted from RFC 5050.](./DTN & Bundle Protocol - Notebook_files/Bundle_Protocol_Data_Unit.png)](http://couble.ovh/figures/Bundle_Protocol_Data_Unit.png)Figure 2: Example of a Bundle Protocol Data Unit, extracted from RFC 5050. [[PNG](http://couble.ovh/figures/Bundle_Protocol_Data_Unit.png)]
### Delivery options

There are seveval types of acknoledgement that can be requested by the application agent to the bundle layer.
Each of them are independant and non exclusive.

* Recieved bundle ack
* Forwarded bundle ack
* Application layer ack
* Custody accepted ack
* A few other more

They are used as indicators for the sender, for example to release a node which was storing temporarly a bundle in 'custody' (see below for more details on custody transfers).
On a more practical point of view, acknoledgement are used by application agents to locate and emphasize the progress of the data transfer.

### Quality of service

The Bundle protocol offers two parameters of quality of service:

* an importance degree (or drop me last priority)
* a delay degree (or forward me first)

Both have 3 levels, which gives 9 levels for Quality of service.
  

Altgough expiration time is not expressively a QoS criteria, it has its impact on forwarding queues in intermediary bundle nodes.

### Reliability and Custody transfers

This is in my opinion the main new feature of the DTN.
  

Custody transfers are store-and-forward transfers pushed at their maximum. 
The bundle passes from one custodian endpoint to the other, and each custodian endpoint stores the bundle until it has confirmation that the bundle was accepted in custody by another custodian endpoint.

This is somewhat similar to a prisonier transfer, hence the name. 
  

The bundle/prisonier is escorted from one custodian endpoint/prison to the other by an agent who will expressively report the success of the transfer.
Except the bundle stays in the first prison while also being transferred to the second! Handy, isn't it?
  

Bundle nodes are free to chose to accept or refuse a bundle's custody request. 
If they chose to refuse custody, they still can simply forward it, and send acknoledgements according to the bundle's delivery options. 
  

This is illustrated on the figure below. [A step-by-step figure is available here](http://couble.ovh/figures/custody_transfer_6steps.png)

[![Example of a custody transfer.](./DTN & Bundle Protocol - Notebook_files/DTN_custody.png)](http://couble.ovh/figures/DTN_custody.png)Figure 3: Example of a custody transfer. [[PNG](http://couble.ovh/figures/DTN_custody.png)]
### Application to common L7 protocols, and conclusions

This will be the focus for a future article related to DTN.

---

Sources:

1. [RFC 4838: Delay Tolerant Networking Architectures](https://tools.ietf.org/html/rfc4838)
2. [RFC 5050: Bundle Protocol Specification](https://tools.ietf.org/html/rfc5050)
3. [RFC 6255: Delay Tolerant Networking Bundle Protocol IANA Registries](https://tools.ietf.org/html/rfc6255)
