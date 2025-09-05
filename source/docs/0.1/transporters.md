# Transporters

---

{% note info Go Implementation %}

This documentation has been updated to reflect the Go implementation of Moleculer. All examples are now in Go and show the actual API available in the Go version.
{% endnote %}
Transporter is an important module if you are running services on multiple nodes. Transporter communicates with other nodes. It transfers events, calls requests and processes responses ...etc. If a service runs on multiple instances on different nodes, the requests will be load-balanced among live nodes.

## Built-in transporters

### TCP transporter ![Experimental transporter](https://img.shields.io/badge/status-experimental-orange.svg)

This is a no-dependency, zero-configuration TCP transporter. It uses [Gossip protocol](https://en.wikipedia.org/wiki/Gossip_protocol) to disseminate node statuses, service list and heartbeats. It contains an integrated UDP discovery feature to detect new and disconnected nodes on the network.
If the UDP is prohibited on your network, you can use `urls` option. It is a list of remote endpoints (host/ip, port, nodeID). It can be a static list in your configuration or a file path which contains the list.

> Please note, it is an **experimental** transporter. **Do not use it in production yet!**

<!-- **This TCP transporter is the default transporter in Moleculer**.
It means, you don't have to configure any transporter, just start the brokers/nodes, use same namespaces and the nodes will find each others.
>If you don't want to use transporter, set `transporter: null` in broker options.
-->

**Use TCP transporter with default options**

```go
bkr := broker.New(&moleculer.Config{
    Transporter: "TCP",
})
```

**All TCP transporter options with default values**

```go
bkr := broker.New(&moleculer.Config{
    LogLevel: "info",
    Transporter: "TCP",
    TCPOptions: map[string]interface{}{
        // Enable UDP discovery
        "UdpDiscovery": true,
        // Reusing UDP server socket
        "UdpReuseAddr": true,

        // UDP port
        "UdpPort": 4445,
        // UDP bind address (if empty, bind on all interfaces)
        "UdpBindAddress": "",
        // UDP sending period (seconds)
        "UdpPeriod": 30 * time.Second,

        // Multicast address.
        "UdpMulticast": "239.0.0.0",
        // Multicast TTL setting
        "UdpMulticastTTL": 1,

        // Send broadcast (Boolean, String, Array<String>)
        "UdpBroadcast": []string{},

        // TCP server port. 0 means random port
        "Port": 0,
        // Static remote nodes address list (when UDP discovery is not available)
        "Urls": []string{},
        // Use hostname as preferred connection address
        "UseHostname": true,

        // Gossip sending period in seconds
        "GossipPeriod": 2,
        // Maximum enabled outgoing connections. If reach, close the old connections
        "MaxConnections": 32,
        // Maximum TCP packet size
        "MaxPacketSize": 1 * 1024 * 1024,
    },
})
```

**TCP transporter with static endpoint list**

```go
bkr := broker.New(&moleculer.Config{
    LogLevel: "info",
    Transporter: "TCP",
    TCPOptions: map[string]interface{}{
        "UdpDiscovery": false,
        "Urls": []string{
            "172.17.0.1:6000/node-1",
            "172.17.0.2:6000/node-2",
            "172.17.0.3:6000/node-3",
        },
    },
})
```

_You don't need to set `port` because it find & parse the self TCP port from URL list._

**TCP transporter with shorthand static endpoint list**
It needs to start with `tcp://`.

```go
bkr := broker.New(&moleculer.Config{
    Transporter: "tcp://172.17.0.1:6000/node-1,172.17.0.2:6000/node-2,172.17.0.3:6000/node-3",
})
```

**TCP transporter with static endpoint list file**

```go
bkr := broker.New(&moleculer.Config{
    Transporter: "file://./nodes.json",
})
```

```json
// nodes.json
[
    "127.0.0.1:6001/client-1",
    "127.0.0.1:7001/server-1",
    "127.0.0.1:7002/server-2"
]
```

{% note info Serviceless node %}
Please note, you don't need to list all remote nodes. It's enough at least one node which is online. For example, you can create a "serviceless" gossiper node, which does nothing, just shares other remote nodes addresses by gossip messages. So all nodes need to know only the gossiper node address to be able to communicate with all other nodes.
{% endnote %}

### NATS Transporter ![Stable transporter](https://img.shields.io/badge/status-stable-green.svg)

Built-in transporter for [NATS](http://nats.io/).

> NATS Server is a simple, high performance open source messaging system for cloud-native applications, IoT messaging, and microservices architectures.

```go
import (
    "github.com/moleculer-go/moleculer"
    "github.com/moleculer-go/moleculer/broker"
)

bkr := broker.New(&moleculer.Config{
    Transporter: "nats://nats.server:4222",
})
```

{% note info Dependencies %}
To use this transporter, the NATS Go client is automatically included as a dependency.
{% endnote %}

#### Examples

```go
// Connect to 'nats://localhost:4222'
bkr := broker.New(&moleculer.Config{
    Transporter: "NATS",
})

// Connect to a remote NATS server
bkr := broker.New(&moleculer.Config{
    Transporter: "nats://nats-server:4222",
})

// Connect with options
bkr := broker.New(&moleculer.Config{
    Transporter: "NATS",
    // Additional NATS options can be configured through the transporter factory
})
```

### Redis Transporter ![Stable transporter](https://img.shields.io/badge/status-stable-green.svg)

Built-in transporter for [Redis](http://redis.io/).

```go
import (
    "github.com/moleculer-go/moleculer"
    "github.com/moleculer-go/moleculer/broker"
)

bkr := broker.New(&moleculer.Config{
    Transporter: "redis://redis.server:6379",
})
```

{% note info Dependencies %}
To use this transporter, the Redis Go client is automatically included as a dependency.
{% endnote %}

#### Examples

```go
// Connect with default settings
bkr := broker.New(&moleculer.Config{
    Transporter: "Redis",
})

// Connect with connection string
bkr := broker.New(&moleculer.Config{
    Transporter: "redis://localhost:6379",
})

// Connect with options
bkr := broker.New(&moleculer.Config{
    Transporter: "Redis",
    // Additional Redis options can be configured through the transporter factory
})
```

### AMQP Transporter ![Stable transporter](https://img.shields.io/badge/status-stable-green.svg)

Built-in transporter for [AMQP](https://www.amqp.org/) protocol _(e.g.: [RabbitMQ](https://www.rabbitmq.com/))_.

```go
import (
    "github.com/moleculer-go/moleculer"
    "github.com/moleculer-go/moleculer/broker"
)

bkr := broker.New(&moleculer.Config{
    Transporter: "amqp://rabbitmq-server:5672",
})
```

{% note info Dependencies %}
To use this transporter, the AMQP Go client is automatically included as a dependency.
{% endnote %}

#### Examples

```go
// Connect to 'amqp://guest:guest@localhost:5672'
bkr := broker.New(&moleculer.Config{
    Transporter: "AMQP",
})

// Connect to a remote server
bkr := broker.New(&moleculer.Config{
    Transporter: "amqp://rabbitmq-server:5672",
})

// Connect to a remote server with options & credentials
bkr := broker.New(&moleculer.Config{
    Transporter: "amqp://user:pass@rabbitmq-server:5672",
    // Additional AMQP options can be configured through the transporter factory
})
```

### Kafka Transporter ![Experimental transporter](https://img.shields.io/badge/status-experimental-orange.svg)

Built-in transporter for [Kafka](https://kafka.apache.org/). It is a very simple implementation. It transfers Moleculer packets to consumers via pub/sub. There are not implemented offset, replay...etc features.

> Please note, it is an **experimental** transporter. **Do not use it in production yet!**

{% note info Dependencies %}
To use this transporter, the Kafka Go client is automatically included as a dependency.
{% endnote %}

**Connect to Kafka**

```go
bkr := broker.New(&moleculer.Config{
    LogLevel: "info",
    Transporter: "kafka://192.168.51.29:9092",
})
```

**Connect to Kafka with custom options**

```go
bkr := broker.New(&moleculer.Config{
    LogLevel: "info",
    Transporter: "kafka://192.168.51.29:9092",
    // Additional Kafka options can be configured through the transporter factory
})
```

### Custom transporter

You can also create your custom transporter module. We recommend to copy the source of [NatsTransporter](https://github.com/moleculer-go/moleculer/blob/master/transit/nats/nats.go) and implement the `Connect`, `Disconnect`, `Subscribe` and `Publish` methods.

#### Use custom transporter

```go
import (
    "github.com/moleculer-go/moleculer"
    "github.com/moleculer-go/moleculer/broker"
)

bkr := broker.New(&moleculer.Config{
    TransporterFactory: func() interface{} {
        return &MyAwesomeTransporter{}
    },
})
```

## Disabled balancer

Some transporter server has built-in balancer solution. E.g.: RabbitMQ, NATS. If you want to use the transporter balancer instead of Moleculer balancer, use the `DisableBalancer` broker option.

**Example**

```go
bkr := broker.New(&moleculer.Config{
    DisableBalancer: true,
    Transporter: "nats://some-server:4222",
})
```

{% note info Please note %}
If you disable the built-in Moleculer balancer, all requests & events will be transferred via transporter (including local requests). E.g. you have a local math service and you call `math.add` locally, the request is transferred via transporter.
{% endnote %}
