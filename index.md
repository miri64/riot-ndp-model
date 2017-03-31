# STILL WIP

# Architecture
## Node classes
How the NIB is constructed is greatly dependent on what role the node takes in a network. Analysis of the corresponding RFCs resulted in the following made-up of node classes:

![Node classes](class_nodes.svg)

Depending on the node class certain features are compiled into the RIOT image of the node. This is marked with the corresponding color of the node class:

- Features of all IPv6 hosts are marked <span style="color: #000000; font-weight: bold;">black</span>
- features for IPv6 routers are marked <span style="color: #800000; font-weight: bold;">maroon</span>,
- features for all 6LoWPAN nodes (6LN) are marked <span style="color: #008000; font-weight: bold;">green</span>,
- features for 6LoWPAN routers (6LR) are marked <span style="color: #800080; font-weight: bold;">purple</span>, and
- features for 6LoWPAN border routers (6LBR) are marked <span style="color: #008080; font-weight: bold;">teal</span>.

Additional to their role in the network there are certain features that are beyond orthogonal to these roles:

- Components and behaviors required for the NDP address resolution state-machine (typically provided on non-6LoWPAN hosts and all routers) are marked <span style="color: #808000; font-weight: bold;">olive</span>,
- Nodes that start advertising themselves as routers on start-up are marked <span style="color: #ff6600; font-weight: bold;">orange</span>, and
- Nodes that do not advertising themselves as routers on start-up are marked <span style="color: #ffcc00; font-weight: bold;">gold</span>.

The reasoning for not including the NDP address resolution state-machine on non-routing 6LN is that on these kinds of nodes [non-link-local addresses are always considered off-link and the link-layer addresses of link-local addresses are derived from their IID][6lowpan-host-behavior]. So none of this information needs to be stored.

Furthermore, there are features the user can decide to de-/activate:

- Asynchronous error reporting is marked <span style="color: #ff0000; font-weight: bold;">red</span>
- Queueing of packets for address resolution is marked <span style="color: #00ff00; font-weight: bold;">lime</span>
- Usage of a destination cache is marked <span style="color: #000080; font-weight: bold;">navy</span>
- Stateless Address Autoconfiguration according to [RFC4862] is marked <span style="color: #c0c0c0; font-weight: bold;">silver</span>
- Handling of redirect messages (which requires a destination cache) is marked <span style="color: #2ad4ff; font-weight: bold;">azure</span>

Lastly, there are the components related to [6LoWPAN's two substitutable features][6lowpan-subst-feat]:

- Components related to multihop prefix and context distribution are marked <span style="color: #0000ff; font-weight: bold;">blue</span>, and
- Components related to multihop Duplicate Address Detetection are marked <span style="color: #ff00ff; font-weight: bold;">magenta</span>

[6lowpan-host-behavior]: https://tools.ietf.org/html/rfc6775#section-5.6
[6lowpan-subst-feat]: https://tools.ietf.org/html/rfc6775#section-8
[RFC4862]: https://tools.ietf.org/html/rfc4862

## API
![NIB: API architecture](class_api.svg)

## Implementation
![NIB: Internal architecture](class_impl.svg)

(**OffLinkEntry** also on non-routers for prefix list.)

# Use-cases
## Initialize interface
![Sequence diagram: Interface initialization](seq_nib_use_case_001.svg)

## Determine next hop link-layer address
![Sequence diagram: Next-hop determination](seq_nib_use_case_002.svg)

(Search in **OffLinkEntry**s also on non-routers for prefix list)

### Get link-layer address of route entry
![Sequence diagram: Route entry address resolution](seq_nib_use_case_002.1.svg)

### Address resolution
![Sequence diagram: Address resolution](seq_nib_use_case_002.2.svg)

## Probe neighbor
![Sequence diagram: Probe neighbor](seq_nib_use_case_003.svg)

## Become a router advertising interface
TODO

## Cease to be a router advertising interface
TODO

## Upper-layer reachability confirmation
TODO

## Receive packet event
![Sequence diagram: Receive packet event](seq_nib_use_case_004.svg)

### Handle Neighbor Solicitation
TODO

### Handle Neighbor Advertisement
TODO

### Handle Router Solicitation
TODO

### Handle Router Advertisement
TODO

### Handle Redirect
TODO

### Handle Duplicate Address Request
TODO

### Handle Duplicate Address Confirmation
TODO

## Retransmit Neighbor Solicitation timer event
TODO

## Delayed Neighbor Advertisement timer event
(Advertisement of anycast address)
TODO

## Reconfirm router timer event
TODO

## Reachability timeout timer event
TODO

## Delay timeout timer event
TODO

## Address registration timeout timer event
TODO

## 6LoWPAN Context timeout timer event

## Authoritive Border Router timeout timer event
TODO

## Prefix timeout timer event
TODO

## Route timeout timer event
TODO

## Router timer event
TODO

## Search router timer event
TODO

## Send initial unsolicited Router Advertisement timer event
TODO

## Send unsolicited Router Advertisement timer event
TODO

## Send final unsolicited Router Advertisement timer event
TODO

## Set neighbor cache entry externally
TODO

## Delete neighbor cache entry externally
TODO

## Set prefix externally
TODO

## Delete prefix externally
TODO

## Add forwarding table entry
TODO

## Delete forwarding table entry
TODO

## Set reactive routing callback
TODO
