# GRPC Best Practices With Rust

In recent years, microservices are the most desired architecture among developers. This is because it’s easier to maintain this type of architecture when the code base becomes very large. However, for smaller IT firms, the monolith architecture is the most suited architecture. This way, it’s easier to organize all of their codes together in one place.

Whether monolith or microservice architecture, there’s a need for the application’s backend/server to communicate with its client/front-end. This is exactly what gRPC does. gRPC is an [API (Application Programming Interface)](https://en.wikipedia.org/wiki/API) framework that allows communication between a program in one location on the internet to another program at another location on the internet for processing. In this article, we’ll explore gRPC in Rust, and the best practices to incorporate into your application when using gRPC.

## What Is gRPC

gRPC stands for Google Remote Procedure Call. It is an improvement to the traditional [RPC model](https://en.wikipedia.org/wiki/Remote_procedure_call), projected as the next generation of the RPC infrastructure. This open-source model is initially built by Google. Just like in many RPC models, the gRPC model involves the definition of a service, specifying the service methods that can be called remotely with their parameters and return types. The gRPC server handles client calls, while the client consists of a stub that provides the same methods as the server.

gRPC has major advantages when compared to the traditional RPC model. For instance, it uses [Protocol Buffers](https://en.wikipedia.org/wiki/Protocol_Buffers) as its [interface definition language](https://en.wikipedia.org/wiki/Interface_description_language). This allows developers to work across languages and platforms. gRPC also utilizes the concurrent communication capabilities of [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2) to allow multiple requests from multiple clients and it offers load balancing. It also uses the request-response structure to serve these requests one at a time.

In the next session, we’ll be exploring the different gRPC crates in Rust.

## gRPC Crates in Rust

The Rust programming language is notably still in its early stages. However, the Rust community has developed many gRPC implementations. In this section, we’ll explore these implementations.

### [Tonic](https://crates.io/crates/tonic): 
Tonic is a Rust implementation of gRPC. It comprises three main components: the generic gRPC implementation, the high-performance HTTP/2 implementation, and the codegen powered by [prost](https://github.com/tokio-rs/prost). Prost is a protocol buffer implementation for Rust, which allows developers to write interface definitions that’ll generate Rust codes.

To start with the Tonic crate, add this library to your `cargo.toml` file.

```
tonic = "0.7.1"
```
Next, write your proto, build them and provide your client and server program. Tonic provides TLS support, custom metadata, authentication, and health checking.

### [gRPC-Rust](https://crates.io/crates/grpc)
This is another Rust implementation of the gRPC model. However, this crate is still under development and isn’t suitable for use in production. To use this crate in your project, add it to the dependencies section of your `cargo.toml` file.

```
grpc = "0.8.3"
```

Next, you can proceed to generate Rust codes from your proto files by invoking protoc with the [protoc-rust-grpc crate](https://github.com/stepancheg/grpc-rust/tree/master/protoc-rust-grpc) or with the protoc command and protoc-gen-rust-grpc plugin.

### [gRPCio](https://crates.io/crates/grpcio)

gRPCio is a Rust wrapper of gRPC. Although this crate is still under development, it supports SSL, generic calls, connection level compression, interoperability test, 		QPS benchmark as well as authentication and health check.

To use this crate in your application, add it to the dependency section of your `cargo.toml` file:

```
grpcio = "0.10.2"
```
You can also generate sources from `.proto` files manually.  To do this, follow the steps below:

Install protobuf compiler

```
cargo install protobuf-codegen
```
Install gRPC compiler

```
cargo install grpcio-compiler
```
Generate sources

```
protoc --rust_out=. --grpc_out=. --plugin=protoc-gen-grpc=`which grpc_rust_plugin` example.proto
```
This crate, uses `boringssl` by default to enables support for TLS encryption and some authentication mechanism. You can disable this feature when you don’t need it, by disabling default features.

```
[dependencies]
grpcio = { version = "0.10.2", default-features = false, features = ["protobuf-codec"] }
```
## gRPC Best Practices
gRPC is designed for high performance. In this section, we’ll explore best practices to look out for when designing a system with gRPC in Rust.

### Multiplexing
In programming, multiplexing involves the use of a single in-memory resource to handle multiple external resources. We can achieve this by reusing channels when making gRPC calls. This is important as it improves the overall speed of task completion. When we create a new channel for each gRPC call, each call will require multiple trips between the client and the server to create a new HTTP/2 connection.

For instance, the code below is a better practice since we’re using the same channel instead of shutting it down and restarting another for each call:

```

```
gRPC channels are safe to share and reuse between calls. 

### Load Balancing
Load balancing is an important feature in programming. It involves efficiently distributing incoming network traffic across a group of backend servers. This feature is provided by the gRPC framework. However, only gRPC calls can be load-balanced between endpoints. Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.

Since gRPC utilizes HTTP/2, there’s a possibility of a lack of load balancing since HTTP/2 multiplexes multiple calls on a single TCP connection. Developers can implement a client-side load balancing. With this method, the client knows of each endpoint. Therefore, each gRPC call selects a different endpoint to send the call to. An example of this is utilizing `ginepro` – an add-on to `tonic's Channel` which provides service discovery to perform client-side look-aside gRPC load balancing.  

```
// Using the `LoadBalancedChannel`.
use ginepro::LoadBalancedChannel;
use ginepro::pb::tester_client::TesterClient;

// Build a load-balanced channel given a service name and a port.
let load_balanced_channel = LoadBalancedChannel::builder(
    ("my_hostname", 5000)
  )
  .await
  .expect("Failed to initialise the DNS resolver.")
  .channel();

// Initialise a new gRPC client for the `Test` service
// using the load-balanced channel as transport
let grpc_client = TestClient::new(load_balanced_channel);
```

Another method of implementing load balancing in a streaming gRPC call is the use of a proxy like [ARLB](https://github.com/another-rust-load-balancer/another-rust-load-balancer#:~:text=ARLB%20%28Another%20Rust%20Load%20Balancer%29%20is%20a%20reverse,merely%20a%20proof%20of%20concept%20and%20university%20project.).

### Connection Concurrency
A concurrent connection means the maximum number of connections your server can handle at a time. Applications with high load, or long-running streaming gRPC calls, could see performance issues due to queuing calls. This happens when the number of active calls reaches the connection stream limit.

To solve this problem, developers can enable multiple HTTP/2 connections to create more connections for their calls and avoid queuing.

## Conclusion
In this article, we have explored gRPC and the best practices to have in mind when working with the gRPC framework in Rust. While gRPC allows developers to generate interface designs with any language of their choice, it isn’t a bed of roses.

For instance, there’s a lack of support for additional content types. For example, other content types like image uploads are not supported out-of-the-box as with standard HTTP + REST-based APIs.

Nevertheless, gRPC is a great fit for teams who have worked on the traditional RPC model and need to spice things like performance up. If you want to learn more about gRPC, the [official documentation site](https://grpc.io/docs/guides/performance/) is a great fit for you.
