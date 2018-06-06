# StellarStation API

The public API definition for [StellarStation](https://www.stellarstation.com/) and supported client
libraries / helpers.

This repository is currently under construction and is provided for reference. API implementation is
in progress and documentation will continue to evolve. Feel free to send PRs to improve
documentation when things are unclear or file issues with questions on usage.

## Usage

The StellarStation API is based on [gRPC](https://grpc.io). An API client can be written in any
language supported by gRPC by following one of the language-specific guides [here](https://grpc.io/docs/).

The main protocol definition used to generate language specific stub code is [here](./api/src/main/proto/stellarstation/api/v1/stellarstation.proto).

In addition, we provide precompiled client stubs for Java. Java users can just add a dependency on
the stubs and don't need to compile the protocol into code themselves.

Gradle users should add the `stellarstation-api` artifact to their `dependencies`, e.g.,

```groovy
dependencies {
    compile 'com.stellarstation.api:stellarstation-api:0.0.3'
}
```

Maven users would add to their `pom.xml`

```xml
<dependencies>
  <dependency>
    <groupId>com.stellarstation.api</groupId>
    <artifactId>stellarstation-api</artifactId>
    <version>0.0.3</version>
  </dependency>
</dependencies>
```

A full example of a Java API client can be found [here](./examples/printing-client).

## Authentication

Authentication to the StellarStation API is done using JWT bearer tokens (https://jwt.io). When
initializing an API client, make sure to register call credentials using the private key downloaded
from the StellarStation Console. Details for registering call credentials on a gRPC stub can be
found [here](https://grpc.io/docs/guides/auth.html). Note that if the key has been revoked on the
console, it will not be usable to authenticate with the API.

The `grpc-auth` and `google-auth-library-oauth2-http` libraries can be used to easily setup
authentication of an API client.

```java
// Load the private key downloaded from the StellarStation Console.
ServiceAccountJwtAccessCredentials credentials =
    ServiceAccountJwtAccessCredentials.fromStream(
        Resources.getResource("api-key.json").openStream(),
        URI.create("https://api.stellarstation.com"));

// Setup the gRPC client.
ManagedChannel channel =
    ManagedChannelBuilder.forAddress("localhost", 8081)
        .build();
StellarStationServiceStub client =
    StellarStationServiceGrpc.newStub(channel)
        .withCallCredentials(MoreCallCredentials.from(credentials));
```

## Usage

When using `proto` files from this repository directly in client code, make sure to only use [tagged releases](https://github.com/infostellarinc/stellarstation-api/releases).
Using `proto` files from any non-tagged revision will likely not work correctly.
