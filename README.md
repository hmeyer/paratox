paratox
=======

anonymous, secure communication for post democratic people


Basics
======
- [ECC](http://en.wikipedia.org/wiki/Elliptic_curve_cryptography)  and [ECMQV](http://en.wikipedia.org/wiki/ECMQV) is used throughout paratox
- whenever possibile session keys are generated using ECMQV and AES is used (establishing perfect forward secrecy)
- ECC is used directly if one of the peers is not reachable, thus giving of perfect forward secrecy (THIS SHOULD BE MADE VISIBLE IN THE CLIENT)
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

Goals
=====
- AccountIDs should not be linkable to IPs
- Nobodys except Alice and Bob should be able to know that Alice is communicating with Bob
