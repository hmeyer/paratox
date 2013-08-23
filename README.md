paratox
=======

anonymous, secure communication for post democratic people

Goals
=====
- enable private communication while Eve is watching
  - Eve should not know that Alice and Bob are communicating with each other
  - Eve should not know what Alice and Bob are talking about
- have perfect forward secrecy whenever possible (afaik perfect forward secrecy is not possible if Bob wants to send a message to Alice, who is offline)
- Bob and Alice should only not need to rely on single points of trust

Why paratox
===========
- Why not Skype/Yahoo Chat/ICQ/WhatsApp/Jabber?
  - the server always can tell who Alice is communicating with
  - Traffic is usually SSL-encrypted. But only on the transport layer. The server decrypts all packets.
  - OTR fixes the encryption problem, but introduces the requirement that both Alice and Bob have to be online at the same time
    - PGP/GPG/[XEP-0027](http://xmpp.org/extensions/xep-0027.html) would fix this, but it's only available for a limited set of clients
  - and still: the server can tell who is talking to whom
- Why not [TorChat](https://github.com/prof7bit/TorChat)?
  - I like the TorChat idea a lot. But:
  - AFAIK there is no offline message delivery - both peers need to be online at the same time
  - Tor is a versatile tool for a multitude of purposes, therefore there a some security compromises that might weaken privacy:
    - if Eve is able to watch the whole network she might be able to follow a packet from node to node because of it's signature (size) and the timing of incoming and outgoing traffic of the nodes. This way Eve might be able to learn who Alice is talking to. And when.
- Why not [BitMessage](https://bitmessage.org/)?
  - BitMessage is a quite clever take on the privacy aspects of personal information retrieval.
  - While privacy and security should be quite high in theory, BitMessage creates a lot of traffic which is not desired e.g. in a mobile context.
  - BitMessage uses proof of work, which will eat up your phones battery very quickly. Also it will cause your phone to crunch numbers for 2 min before sending a message out. An interactive chat might be difficult this way.
- Why not XYZ?
  - I just didn't know XYZ is solving this issue. Please give me a hint in the [issues](https://github.com/hmeyer/paratox/issues)!


Basics
======
- [ECC](http://en.wikipedia.org/wiki/Elliptic_curve_cryptography)  and [ECMQV](http://en.wikipedia.org/wiki/ECMQV) is used throughout paratox
- whenever possibile session keys are generated using ECMQV and AES is used (establishing perfect forward secrecy)
- ECC is used directly if one of the peers is not reachable, thus giving up perfect forward secrecy (THIS SHOULD BE MADE VISIBLE IN THE CLIENT)
- IDs represent ECC public keys
- Nodes have a temporary NodeID that is created randomly on each startup, and recreated once in a while
- Accounts have an AccountID that is the identifier of the account
- ALL Packets are encrypted (either with ECC or AES (key generated via ECMQV))
- ALL Packets have the same size (which size?) filled up with random bytes (before encryption) or zeros (after encryption)
- communication is done UDP only, using [UDP hole punching](http://en.wikipedia.org/wiki/UDP_hole_punching)
- NodeIDs are bound to IP via DHT (preferrable [Bamboo](http://bamboo-dht.org/), any other might work as well)
- DHT accepts search requests: find XYZ might result in found XYY->1.2.3.4 (which implies XYZ does not exist)
- Packets are Onion-Routed like [Tor](https://www.torproject.org/) does
- new Nodes are found by searching for random node ids
  - Problem: maybe Bob is locked in a net owned by Eve and Eve returns only Eves nodes
  - Solution: Bob has to know multiple entry nodes, from each entry he asks for know online nodes from other entries. If those are returned as non-existent the respective entry node is marked flawed (maybe even published as flawed?)
- AccountIDs are published like [Tor-Hidden-Services](https://www.torproject.org/docs/hidden-services.html.en)
- if Bob is offline, the message to bob will be stored in a nodes close to Bobs AccountId
- Packages will be acknowledged (all of them?)
- each node sends noise traffic to its peers in order to obfuscate real traffic from noise
