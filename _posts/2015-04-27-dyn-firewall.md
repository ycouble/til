---
title: Dynamic Firewalling
description: Securing Multimedia Dynamic Protocols
date: April 27, 2015
categories: [security  ]
---




With the now very common Triple Play, Voice over IP and Video Streaming applications are widely deployed and used. And as many events reminded us, securing applications is a critical matter nowadays.
  

However, deploying firewalling solutions when VoIP or Streaming applications are present on the network can be very risky as it is very hard for the network administrator to have feedback on the user experience.
In this article, we'll try to sum up a few important points on how to support VoIP and Streaming applications passing through a firewall.

### Context & available Solutions

VoIP services often use the Session Initiation Protocol (SIP, specified in RFC 3261 & RFC 3265), the other protocols (MGCP, H323 and others) are still used but are progressively less used by end-users.
  

Streaming and videoconference services on the other may use SIP, RTSP, or even HTTP to set up their media sessions. We will focus here on SIP, but the remarks here are also applicable to other protocols.

#### Dynamic protocols

VoIP and Streaming are seen as dynamic protocol because the media payload is often sent on a different channel than the session information. 
This is inherited from Legacy telephony with separated control and data planes.
  

It generally looks like this:

[![Media session setup](./Dynamic Firewalling - Notebook_files/dynamic_protocol.png)](https://web.archive.org/web/20180904000145/http://couble.ovh/figures/dynamic_protocol.png)Figure 1: Media session setup [[PNG](https://web.archive.org/web/20180904000145/http://couble.ovh/figures/dynamic_protocol.png)]
#### SIP

SIP is a dynamic protocol, where one or more sessions are negotiated by two user-agents before starting to send the user data (here, voice of video).
  

A classical SIP conversation between two direct agents will look like this:

[![Simple SIP conversation](./Dynamic Firewalling - Notebook_files/SIP_session.png)](https://web.archive.org/web/20180904000145/http://couble.ovh/figures/SIP_session.png)Figure 2: Simple SIP conversation [[PNG](https://web.archive.org/web/20180904000145/http://couble.ovh/figures/SIP_session.png)]

SIP generally relies on the Session Description Protocol -which is more a language to describe a media session than a protocol- to negotiate the parameters of the session.
  

SDP is carried in some SIP packets (e.g. INVITEs, 200 OKs, ACKs), and provides information such as UDP ports which will be used to send/receive the media content.

#### Allowing SIP and its content through a firewall

On a firewall, allowing VoIP will first require the admin to authorize the SIP protocol, which classically uses UDP port 5060 both ways.
  

But then, how do we, as the admin, allow the voice/video flows?

* A simple solution is to authorize a window of UDP ports that we know will be assigned to the media flows.
   

 These ports are generally assigned within configurable limits and asking the person in charge of the VoIP solution may be sufficient to make it work.
 But this solution does not satisfy high security requirements as thousands of ports are potentially open and uncontrolled.
* A more complex solution is to implement statefulness in the firewall, and by inspecting the SIP/SDP payload, to identify "on the fly" which ports need to be open and to open them only for the duration of the call.

### Consequences of dynamic firewalling

#### Hardware

This type of operation requires to have Deep Inspection capacities, and enough resource to save and maintain a state table of each session. 
In other words, a big CPU and enough RAM, as well as a full implementation of every dynamic protocol.
  

And therefore higher end firewalls, obviously.

#### Impact on other functions

On next generation firewalls, other functions such as IPS (Intrusion Protection System), Application control, DoS prevention may be activated on dynamic protocol flows, and must be adapted to support the media flow.
  

For example, the application control, which is in charge to check the authenticity of the application headers of a packet, will need to be aware that a new type of payload needs to be authorized.

#### DoS Protection

With dynamic firewalling, it is possible to dynamically identify which flows are legitimate media flows and which ones may be malicious ones. 
Thus, it enables the admin to apply different policies to identified media sessions and other generic unknown packets. 
  
Also, by isolating media session flows in a dedicated DoS policy, one may ensure that none of these packets are impacted by a global DoS attack (udp flood for example)

#### Stateful firewall clusters

When the firewall is redundant/load-balanced, what happens when the master firewall fails?
  

With the simple solution, both firewalls have the same simple configuration and media streams are not interrupted.
But when dynamic management of sessions is implemented and configured, it is a bit more complicated.
  

All sessions must be synchronized between the firewall who initiated the session and the other firewalls, so that when it fails, the firewall which will inherit of the session knows which ports must be open for the media streams.
This is a pretty important feature to be developped when dynamic session management is used, or the service user experience may be seriously degraded in case of a failure (all calls/media streams interrupted at once).

### Example on FortiOS

Let's have a look at how it is implemented in FortiOS, the firewall OS for Fortinet products (Fortigate series).

#### Service configuration

##### Service customization

SIP is already configured by default on FortiOS, so you don't need to modify it. However, if you need to use specific ports for your SIP application, it is possible to change it manually.
`config vdom
 edit test
 config firewall service custom
 edit "SIP-custom"
 set protocol TCP/UDP/SCTP
 set udp-portrange **<SIP\_custom\_port>**
 set comment "custom SIP service"
 next
 end
 next
end`
NB: if VDOM are not used, use `config global` instead.

##### Session-helpers

Each manufacturer has its own name for this feature, Fortinet uses the words *session helpers*.
Session helpers are implemented directly by the manufacturer and may not be configurable, or limited in configurability.
For fortiOS, it is only possible to change the service port (the one the session protocol is using).
  

To display all session-helpers:
`config global
 show system session-helper`
And then look for the block with sip as name:
 `edit 13
 set name sip
 set port 5060
 set protocol 17
 next`
**NB: if you need to customize or duplicate a session-helper entry, it is important not to change the name, which is how FortiOS associates the session-helper with the actual code.**

##### Policy

The association between a service and its session-helpers is done automatically.
  

The last step for the basic configuration is to create the policy:
`config vdom
 edit test
 config firewall policy
 edit 1
 set srcintf **<portX>**
 set dstintf **<portY>**
 set srcaddr **<addr\_source>**
 set dstaddr **<addr\_destination>**
 set service SIP *//or the name of the created custom service*
 set action accept
 next
 end
 next
end`

#### Application Control

If a strict Application profile is applied to your policy (which is only possible on high end devices), it may be necessary to add the RTP/RTCP or any media transport protocol used to you application control list.

#### IPS

Similarly, you may want to monitor IPS threats regarding the media protocols and associate the IPS/Application Control profiles to the policy.

#### Dos Policies

DoS policies are better used when they are more specific: if a type of packet crosses its associated thresholds, packets will start to be dropped by the firewall.
  

With session-helpers, the media flows are identified as belonging to the same service as the SIP, and are treated as such by the DoS protection mecanisms.
Therefore it is necessary to dimension the DoS policy in a consistent way, including both SIP and RTP traffic, which may represent high udp packet rates.
  

(with G711.A codex, 1 unidirectional media stream generates about 45 pps, versus maximum 2 or 3 pps when SIP is alone)

#### High availability parameters

In FortiOS, high availability and session synchronization settings for media traffic are setup as follow:
`config global
 config system ha
 set session-pickup enable
 set session-pickup-connectionless enable
 end
end`

Sources:

1. [RFC 3261: SIP: Session Initiation Protocol](https://web.archive.org/web/20180904000145/https://www.ietf.org/rfc/rfc3261.txt)
2. [FortiOS technical documentation](https://web.archive.org/web/20180904000145/http://docs.fortinet.com/)


 * [Context & available Solutions](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-3-0)
	+ [Dynamic protocols](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-0)
	+ [SIP](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-1)
	+ [Allowing SIP and its content through a firewall](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-2)
* [Consequences of dynamic firewalling](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-3-1)
	+ [Hardware](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-3)
	+ [Impact on other functions](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-4)
	+ [DoS Protection](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-5)
	+ [Stateful firewall clusters](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-6)
* [Example on FortiOS](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-3-2)
	+ [Service configuration](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-7)
		- [Service customization](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-5-0)
		- [Session-helpers](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-5-1)
	+ [Application Control](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-5-2)
	+ [IPS](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-5-3)
	+ [Dos Policies](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-9)
	+ [High availability parameters](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#toc-4-10)

[Sources](https://web.archive.org/web/20180904000145/http://couble.ovh/Dynamic-Firewalling.html#references)

---