# What is Moleculer?

---

Moleculer is a simple, fast, modern and powerful microservices framework written in Go (https://golang.org/).

It helps you to build simple, efficient, reliable & scalable services.

Easy to go from 1 machine to many machines. Easy to focus on your problem.

## Example

```go
package main

import (
	"fmt"

	"github.com/moleculer-go/moleculer"
	"github.com/moleculer-go/moleculer/broker"
)

type MathService struct {
}

func (s MathService) Name() string {
	return "math"
}

func (s *MathService) Add(params moleculer.Payload) int {
	return params.Get("a").Int() + params.Get("b").Int()
}

func (s *MathService) Sub(a int, b int) int {
	return a - b
}

func main() {
	var bkr = broker.New(&moleculer.Config{LogLevel: "error"})
	bkr.Publish(&MathService{})
	bkr.Start()
	result := <-bkr.Call("math.add", map[string]int{
		"a": 10,
		"b": 130,
	})
	fmt.Println("result: ", result.Int())
	//$ result: 140
	bkr.Stop()
}
```

# Roadmap

![Timeline](/images/timeline.png)

## v0.1.0 (MVP)

Development is `complete` - Documentation is `in-progress` and benchmark is also `in-progress`.

**Contents:**

-   Service Broker
-   Transit and Transport
-   Actions (request-reply)
-   Events
-   Mixins
-   Load balancing for actions and events (random round-robin)
-   Service registry & dynamic service discovery
-   Versioned services
-   Middlewares
-   TCP Transporter (with UDP discovery)
-   NATS Transporter
-   Redis Transporter
-   AMQP Transporter
-   Kafka Transporter
-   JSON Serializer
-   Examples :)

## v0.2.0 (Beta RC1)

-   Action validators
-   Support for streams
-   More Load balancing implementations (cpu-usage, latency)
-   Fault tolerance features (Circuit Breaker, Bulkhead, Retry, Timeout, Fallback)
-   Built-in caching solution (memory, Redis)
-   More transporters (gRPC, TCP, Redis, Kafka)
-   More serializers (Avro, MsgPack, Protocol Buffer, Thrift)

## v0.3.0 (Beta)

-   Performance and Optimization
-   More DB Adaptors (SQLLite, Firebase, MySQL)
-   CLI for Project Seed Generation

## v0.4.0 (Alpha)

-   Event Sourcing Mixins

## v0.5.0 (Release)

# Installation

```bash
$ go get github.com/moleculer-go/moleculer
```

# Running examples

```bash

# simple math service example
$ go run github.com/moleculer-go/moleculer/examples/standalone

# math service with events
$ go run github.com/moleculer-go/moleculer/examples/math

# Redis transporter example
$ go run github.com/moleculer-go/moleculer/examples/redis_transporter

# AMQP transporter example
$ go run github.com/moleculer-go/moleculer/examples/amqp_transporter

```

{% note info Versioning %}
Until Moleculer reaches a `1.0` release, breaking changes will be released with a new minor version. For example `0.1.1`, and `0.1.4` will be backward compatible, but `0.2.0` will have breaking changes.
{% endnote %}
