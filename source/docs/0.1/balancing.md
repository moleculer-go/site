# Load balancing

---

{% note info Go Implementation %}

This documentation has been updated to reflect the Go implementation of Moleculer. All examples are now in Go and show the actual API available in the Go version.
{% endnote %}
Moleculer has several built-in load balancing strategies. If services have multiple running instances, ServiceRegistry uses these strategies to select a node from all available nodes.

## Built-in strategies

To configure strategy, set `strategy` broker options under `registry` property. It can be either a name (in case of built-in strategies) or a `Strategy` class which inherited from `BaseStrategy` (in case of custom strategies).

**Configure balancing strategy**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.RandomStrategy{}
    },
})
```

### RoundRobin strategy

This strategy selects a node based on [round-robin](https://en.wikipedia.org/wiki/Round-robin_DNS) algorithm.

**Usage**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.RoundRobinStrategy{}
    },
})
```

### Random strategy

This strategy selects a node randomly.

**Usage**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.RandomStrategy{}
    },
})
```

### CPU usage-based strategy

This strategy selects a node which has the lowest CPU usage. Due to the node list can be very long, it gets samples and selects the node with the lowest CPU usage from only samples instead of the whole node list.

**Usage**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.CpuUsageStrategy{}
    },
})
```

**Strategy options**

| Name          | Type     | Default | Description                                                                                                |
| ------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------- |
| `sampleCount` | `Number` | `3`     | the number of samples. _To turn of sampling, set to `0`._                                                  |
| `lowCpuUsage` | `Number` | `10`    | the low CPU usage percent (%). The node which has lower CPU usage than this value is selected immediately. |

**Usage with custom options**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.CpuUsageStrategy{
            SampleCount: 3,
            LowCpuUsage: 10,
        }
    },
})
```

### Latency-based strategy

This strategy selects a node which has the lowest latency, measured by periodic ping commands. Notice that the strategy only ping one of nodes from a single host. Due to the node list can be very long, it gets samples and selects the host with the lowest latency from only samples instead of the whole node list.

**Usage**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.LatencyStrategy{}
    },
})
```

**Strategy options**

| Name           | Type     | Default | Description                                                                                                                           |
| -------------- | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `sampleCount`  | `Number` | `5`     | the number of samples. If you have a lot of hosts/nodes, it's recommended to _increase_ the value. _To turn of sampling, set to `0`._ |
| `lowLatency`   | `Number` | `10`    | the low latency (ms). The node which has lower latency than this value is selected immediately.                                       |
| `collectCount` | `Number` | `5`     | the number of measured latency per host to keep in order to calculate the average latency.                                            |
| `pingInterval` | `Number` | `10`    | ping interval (s). If you have a lot of host/nodes, it's recommended to _increase_ the value.                                         |

**Usage with custom options**

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &strategy.LatencyStrategy{
            SampleCount:  15,
            LowLatency:   20,
            CollectCount: 10,
            PingInterval: 15,
        }
    },
})
```

## Custom strategy

Custom strategy can be created. We recommend to copy the source of [RandomStrategy](https://github.com/moleculer-go/moleculer/blob/master/strategy/random.go) and implement the `Select` method.

### Create custom strategy

```go
type MyStrategy struct {
    // Add any custom fields here
}

func (s *MyStrategy) Select(list []Node) Node {
    // Implement your selection logic here
    return list[0] // Example: return first node
}

```

### Use custom strategy

```go
bkr := broker.New(&moleculer.Config{
    StrategyFactory: func() interface{} {
        return &MyStrategy{}
    },
})
```
