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

Note, that any node can have any of these classes at any point. This is why additional dynamic checks on an interface if it really is e.g. a 6LoWPAN interface are needed.

Additional to their role in the network there are certain features that are beyond orthogonal to these roles:

- Components and behaviors required for the NDP address resolution state-machine (typically provided on non-6LoWPAN hosts and all routers) are marked <span style="color: #808000; font-weight: bold;">olive</span>,
- Nodes that start advertising themselves as routers on start-up are marked <span style="color: #ff6600; font-weight: bold;">orange</span>, and

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

* NIB_FT::iter(nextHop) allows for generation of source route trees (default: NULL, for all entries)

## Implementation
![NIB: Internal architecture](class_impl.svg)

OnLinkEntry::addNC() treats the **OnLinkEntry** collection as a cache:

![Activity diagram: OnLinkEntry::addNC()](act_onlinkentry_addnc.svg)

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
![Sequence diagram: Become a router advertising interface](seq_nib_use_case_004.svg)

## Cease to be a router advertising interface
![Sequence diagram: Cease to be a router advertising interface](seq_nib_use_case_005.svg)
TODO

## Upper-layer reachability confirmation
TODO

## Receive packet event
![Sequence diagram: Receive packet event](seq_nib_use_case_007.svg)

### Handle Neighbor Solicitation
![Sequence diagram: Handle neighbor solicitation](seq_nib_use_case_007.01.svg)
(nbr_sol_t::isValid(), see [RFC4861])
(flags see [RFC4861])

### Handle Neighbor Advertisement
![Sequence diagram: Handle neighbor advertisement](seq_nib_use_case_007.02.svg)
(nbr_adv_t::isValid(), see [RFC4861])

### Handle Router Solicitation
![Sequence diagram: Handle router solicitation](seq_nib_use_case_007.03.svg)
(rtr_sol_t::isValid(), see [RFC4861])

### Handle Router Advertisement
![Sequence diagram: Handle router advertisement](seq_nib_use_case_007.04.svg)
(rtr_adv_t::isValid(), see [RFC4861])

### Handle Redirect
TODO

### Handle advertised link-layer
![Sequence diagram: Handle advertised link-layer (TLLAO)](seq_nib_use_case_007.06.svg)

### Handle Duplicate Address Request
TODO

### Handle Duplicate Address Confirmation
TODO

### Handle Source Link-Layer Address Option
![Sequence diagram: Handle Source Link-Layer Address Option](seq_nib_use_case_007.09.svg)

### Handle Prefix Information Option
TODO

### Handle Redirect Header Option
(TLLAO => if NCE created: set STALE)
TODO

### Handle MTU Option
TODO

### Handle Address Registration Option
![Sequence diagram: Handle Address Registration Option](seq_nib_use_case_007.13.svg)
(nbr_sol_t::isValid(), see [RFC6775])
(x == 'before the Lifetime expires')

### Register address upstream
![Sequence diagram: Register address upstream](seq_nib_use_case_007.14.svg)

### Handle 6LoWPAN Context Option
TODO

### Handle Authoritative Border Router Option
TODO

## Search router
![Sequence diagram: Search router](seq_nib_use_case_008.svg)

## Advertise router
![Sequence diagram: Advertise router](seq_nib_use_case_009.svg)

## Retransmit Neighbor Solicitation timer event
![Sequence diagram: Receive packet event](seq_nib_use_case_010.svg)
(State should be none of the above [UNMANAGED == 0] when OnLinkEntry not in NIB because of previous deletion)
(Two different types to differentiate in timer, otherwise the same)
(PROBE behavior according to [RFC7048])

[RFC7048]: https://tools.ietf.org/html/rfc7048

## Delayed Neighbor Advertisement timer event
![Sequence diagram: Delayed Neighbor Advertisement timer event](seq_nib_use_case_011.svg)

## Search router timer event
![Sequence diagram: Search router timer event](seq_nib_use_case_012.svg)

## Reconfirm router timer event
![Sequence diagram: Reconfirm router timer event](seq_nib_use_case_013.svg)

## Reachability timeout timer event
![Sequence diagram: Reachability timeout timer event](seq_nib_use_case_014.svg)

## Delay timeout timer event
![Sequence diagram: Delay timeout timer event](seq_nib_use_case_015.svg)

## Address registration timeout timer event
![Sequence diagram: Address registration timeout timer event](seq_nib_use_case_016.svg)

## 6LoWPAN Context timeout timer event
TODO

## Authoritive Border Router timeout timer event
TODO

## Prefix timeout timer event
TODO

## Route timeout timer event
TODO

## Router timeout timer event
TODO

## Send unsolicited Router Advertisement timer event
![Sequence diagram: Send unsolicited Router Advertisement timer event](seq_nib_use_case_022.svg)
sendRA() sets iface::lastMCRA to current time in milliseconds

## Send solicited Router Advertisement timer event
![Sequence diagram: Send solicited Router Advertisement timer event](seq_nib_use_case_023.svg)

## Recalculate Reachable Time
TODO (see https://tools.ietf.org/html/rfc4861#section-6.3.4)

## Wake-up from sleep
TODO

## Add address to interface
TODO

## Remove address from interface
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

## Set 6LoWPAN compression context
TODO

## Remove 6LoWPAN compression context
TODO

[RFC4861]: https://tools.ietf.org/html/rfc4861
[RFC6775]: https://tools.ietf.org/html/rfc6775
