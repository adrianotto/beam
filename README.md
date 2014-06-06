# Beam

## A library to break down an application into loosely coupled micro-services

Beam is a library to turn your application into a collection of loosely coupled micro-services.
It implements an ultra-lightweight hub for the different components of an application
to discover and consume each other, either in-memory or across the network.

Beam can be embedded with very little overhead by using Go channels. It
also implements an efficient http2/tls transport which can be used to
securely expose and consume any micro-service across a distributed system.

Because remote Beam sessions use regular HTTP/2.0 over TLS sessions, they can
be used in combination with standard HTTP/2.0 proxy or authentication
middleware. This means Beam, when configured propely, can be safely exposed
on the public Internet. It can also be embedded in an existing rest API
using an HTTP/1.0 and websocket fallback.

## How is it different from RPC or REST?

Modern micro-services are not a great fit for classical RPC or REST
protocols because they often rely heavily on events, bi-directional
communication, stream multiplexing, and some form of data synchronization.
Sometimes these services have a component which requires raw socket access,
either for performance (file transfer, event firehose, database access) or
simply because they have their own protocol (dns, smtp, sql, ssh,
zeromq, etc). These components typically need a separate set of tools
because they are outside the scope of the REST and RPC tools. If there is
also a websocket or ServerEvents transport, those require yet another layer
of tools.

Instead of a clunky patchwork of tools, Beam implements in a single
minimalistic library all the primitives needed by modern micro-services:

* Request/Response Message Pattern
    * Allows arbitrary structured data
    * Requests and responses can flow in any direction, and can be arbitrarily nested, for example to implement a self-registering worker model
    * Any request or response can include any number of streams, multiplexed in both directions on the same session

* Asynchronous Event Message Pattern
    * Events may flow in real-time in both directions

* Pluggable Message Serialization Interface
    * Any message serialization format can be plugged in: 
        * JSON
        * XML
        * MessagePack
        * Protobuf
        * Optional minimalist key/value format, designed to be:
            * Extremely fast to serialize and parse
            * Dead-simple to implement
            * Suitable for one-time data copy, file storage, and real-time synchronization

* File Descriptor Support
    * Raw file descriptors can be "attached" to any message, and passed under the hood using the best method available to each transport:
        * Go channel transport just passes os.File pointers around
        * Unix socket transport uses fd passing which makes it suitable for high-performance IPC
        * TCP transport uses dedicated HTTP/2.0 streams
        * (Bonus Extension): A built-in TCP gateway
            * Can be used to proxy raw network sockets without extra overhead. 
            * That means Beam services can be used as smart gateways to an sql database, ssh or file transfer service, with unified auth, discovery and tooling and without performance penalty.

## Design Philosophy

An explicit goal of Beam is simplicity of implementation and clarity of
spec. Porting it to any language should be as effortless as humanly
possible.

## Creators

**Solomon Hykes**

- <http://twitter.com/solomonstre>
- <http://github.com/shykes>

## Copyright and License

Code and documentation Copyright 2013-2014 Docker, Inc. 
Code released under the Apache 2.0 license.
Docs released under Creative Commons.
