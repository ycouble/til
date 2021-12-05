---
title: MPLS - Label Switching
description: How MPLS lives with other protocols
date: March 3, 2015
categories: [networking]
image: images/blog/label.jpg
---




This article focuses on label switching principles in MPLS protocol, this is the second part of my wiki for the IP/MPLS technology.
See [my introduction the MPLS](http://couble.ovh/MPLS-basics.html) for a wider view on MPLS.

In this article, we will go deeper into what constitues a MPLS network, and how data is switched over the network.


MPLS is a core network protocol and doesn't define how access is to be handled. 
Hence, a provider using MPLS will be interconnected to one or more access operators, who are then its customers. 
  

With that in mind, the router interconnecting the core network provider and the access operator is the Customer Edge Router (CER or more generally only CE). 
On the other hand, routers participating to the MPLS, but only connected to other MPLS routers are called Provider Edge Routers (PER or PE).
  

The MPLS standards [RFC 3031] define new denominations that we will prefer here. 
A MPLS router is called a Label Switch Router (LSR), and when it is on the edge of the MPLS network, it will be called E-LSR (Edge-LSR) or sometimes LER (Label Edge Router).
  

The CE/PE terminology is inherited from an older protocol with similar node roles, ATM.
Operator were used to these names and MPLS devices generally implemented ATM as well, so the old names stayed rather than being replaced by the newly defined ones.

[![A MPLS network, with 3 customers.](./MPLS - Label Switching - Notebook_files/mpls_roles.png)](http://couble.ovh/figures/mpls_roles.png)Figure 1: A MPLS network, with 3 customers. [[PNG](http://couble.ovh/figures/mpls_roles.png)]

The E-LSR or LER is the node in charge of encaplusating/decapsulating packets into/out of the MPLS network, and the LSR does the switching according to the MPLS header.
Classically, a LSR offers more functionalities than a LER. Sometimes, a LER can also fulfill the LSR role.
  

Figure 1 shows a MPLS Network, interconnected with 3 customers. Customer 1 has 2 geographically distant sites. 
The LER/LSR 1 here is the entry point, and hence is a LER, for customer 1 but can also be used as a LSR to switch MPLS frames from LER 2 to LER 3.


MPLS is a packet switching protocol, like Ethernet: ingress packets on port X with label a will always be switched to port Y according to the forwarding table.
  

Except in Ethernet the destination MAC address -which is a unique identifier for an endpoint- is kept from one hop to the other, while MPLS will modify the label at each LSR. This is called label *swapping*.
The meaning of a label is only link-local and doesn't identify anything but a type of packets that are passing from one LSR to the other. 
In fact MPLS doesn't have any endpoint identifier, which retrospectively seems quite obvious for a core network.
  

Hence, packets are not forwarded based on who it is destined to but rather according to how the MPLS network classified it at network ingress.

This is a fairly important distinction and allows MPLS to perform its own Quality of Service independantly from its customers. 
  

Another significant difference between MPLS and Ethernet is that ethernet doesn't use a control plane, which enables an easier automatic decentralized configuration. 
MPLS on the other hand relies on its control protocols to determine what are the label switched paths (LSP), classification, QoS etc.
  

QoS, classification and the control plane are wide topics and will be describe thouroughly later, in articles dedicated to them.
  

Here, let's just focus on how a packet is really forwarded from a MPLS entry point towards its destination network, assuming that all forwarding tables are already computed in the whole network.

Below is a simple example for a 4 node MPLS network and 2 customer networks.
  

Let's also assume the switching tables described in Table 1.

[![Example with 2 LER and 2 LSR](./MPLS - Label Switching - Notebook_files/mpls_path1.png)](http://couble.ovh/figures/mpls_path1.png)Figure 2: Example with 2 LER and 2 LSR [[PNG](http://couble.ovh/figures/mpls_path1.png)]

| Node | ingress port | ingress label | egress port | egress label |
| --- | --- | --- | --- | --- |
| LSR2 | 1 | L0 | 2 | L4 |
| LSR2 | 1 | L1 | 3 | L5 |
|  |  |  |  |  |
| LSR3 | 2 | L4 | 3 | L7 |

Table 1: MPLS forwarding tables for nodes LSR2 & LSR3

Let's say a packet p is routed towards the destination network:

1. At LER1 ingress, packet p is for example *pushed* the label L1 and is forwarded towards LSR2.
2. At LSR2 ingress, p is switched to port 3 and label *swapped* to L5 according to the forwarding table.
3. At LER4 p exits the MPLS network and its label is *popped*.

[![Path for a packet assigned with label L1 at network ingress.](./MPLS - Label Switching - Notebook_files/mpls_path2.png)](http://couble.ovh/figures/mpls_path2.png)Figure 3: Path for a packet assigned with label L1 at network ingress. [[PNG](http://couble.ovh/figures/mpls_path2.png)]

Now assume that another packet p' with different characteristics but towards the same destination network:

1. At LER1 ingress, unlike p, packet p' is *pushed* the label L0 and is forwarded to LSR2.
2. At LSR2 ingress, p' is switched to port 2 and label *swapped* to L4 according to the forwarding table.
3. At LSR3 ingress, p' is switched to port 3 with label L7
4. Ats LER4, p' is label popped, exits and is delivered to the destination network

[![Path for a packet assigned with a different label at network ingress.](./MPLS - Label Switching - Notebook_files/mpls_path3.png)](http://couble.ovh/figures/mpls_path3.png)Figure 4: Path for a packet assigned with a different label at network ingress. [[PNG](http://couble.ovh/figures/mpls_path3.png)]

In this example, we can see that the label a packet is assign will determine how it will be switched all along its path trough the MPLS network.
In this case, p' gets a longer path. 
While it may seem under-optimized traffic handling, it may also be used for two things, which happen to be 2 of the most important feature of MPLS:

* Given a label, one may not access all the MPLS network egress nodes. Also, a customer can be given a label dedicated to him and all his traffic handled the same way, towards its authorized destination. 
 MPLS VPNs are based on that observation.
* If LSR2-LER4 is usually congestioned, p' may have a more advantageous path regarding delay or other metrics.
 Organizing the MPLS network paths according to different constraints is called traffic engineering and is widely used over the internet along with QoS. These two aspects will be further detailed in their respective articles.

To continue with the ethernet comparison, it is noticeable that every ethernet frame with the same destination are always switched using the same path, provided that the network topology doesn't change.


MPLS carries a very small amount of information mainly composed of the label, a TTL and the EXP field, used for QoS. 
Below is the frame format for one label. A MPLS header, called "label stack", is composed of 1 or more labels.

[![MPLS label format.](./MPLS - Label Switching - Notebook_files/mpls_frame.jpg)](http://couble.ovh/figures/mpls_frame.jpg)Figure 5: MPLS label format. [[JPG](http://couble.ovh/figures/mpls_frame.jpg)]

Stacking labels can be used to provide additional services.
For example the Virtual Private Networks based on MPLS use an additional label to identify the 'service' packets belong to additionally to the first layer of labels that are used for switching packets. This, again, will be further explained in a dedicated artcile.


In the case of MPLS over Ethernet, the Ethernet frame at the MPLS ingress node is transported inside the MPLS packet, over another Ethernet frame. (IP over Ethernet over MPLS over Ethernet).
  
Below are the packet detail of an example MPLS packet. The packet is available [here](http://couble.ovh/assets/mpls1label.pcapng). 
Another example can be found [here](http://couble.ovh/assets/mpls2labels.pcapng) (with two labels).

[![Example of MPLS over Ethernet encapsulation (protocol view).](./MPLS - Label Switching - Notebook_files/mpls_wireshark.jpg)](http://couble.ovh/figures/mpls_wireshark.jpg)Figure 6: Example of MPLS over Ethernet encapsulation (protocol view). [[JPG](http://couble.ovh/figures/mpls_wireshark.jpg)]
[![Example of MPLS over Ethernet encapsulation (bit view).](./MPLS - Label Switching - Notebook_files/mpls_encap_eth.jpg)](http://couble.ovh/figures/mpls_encap_eth.jpg)Figure 7: Example of MPLS over Ethernet encapsulation (bit view). [[JPG](http://couble.ovh/figures/mpls_encap_eth.jpg)]

---

Sources:

1. Experience
2. Courses from [B.Paillassa](http://www.researchgate.net/profile/Beatrice_Paillassa/publications) at ENSEEIHT
3. [RFC 3031: MPLS Architecture](https://www.ietf.org/rfc/rfc3031.txt)
4. [RFC 3032: MPLS Label Stack Encoding](https://www.ietf.org/rfc/rfc3032.txt)
5. [Wikipedia](http://en.wikipedia.org/wiki/Multiprotocol_Label_Switching)

