# Serializers

---

{% note info Go Implementation %}

This documentation has been updated to reflect the Go implementation of Moleculer. All examples are now in Go and show the actual API available in the Go version.
{% endnote %}
Transporter needs a serializer module which serializes & deserializes the transferred packets.

**Example**

```go
import (
    "github.com/moleculer-go/moleculer"
    "github.com/moleculer-go/moleculer/broker"
    "github.com/moleculer-go/moleculer/transit/nats"
)

bkr := broker.New(&moleculer.Config{
    NodeID: "server-1",
    Transit: nats.NewNatsTransit(),
    Serializer: "JSON", // Default serializer
})
```

## Built-in serializers

### JSON serializer

This is the built-in default serializer. It serializes the packets to JSON string and deserializes the received data to packet.

```go
bkr := broker.New(&moleculer.Config{
    // Serializer: "JSON" // don't need to set, because it is the default
})
```

### Avro serializer

Built-in [Avro](https://github.com/mtth/avsc) serializer.

```js
const broker = new ServiceBroker({
    serializer: "Avro"
});
```

{% note info Dependencies %}
To use this serializer install the `avsc` module with `npm install avsc --save` command.
{% endnote %}

### MsgPack serializer

Built-in [MsgPack](https://github.com/mcollina/msgpack5) serializer.

```js
const broker = new ServiceBroker({
    serializer: "MsgPack"
});
```

{% note info Dependencies %}
To use this serializer install the `msgpack5` module with `npm install msgpack5 --save` command.
{% endnote %}

### ProtoBuf serializer

Built-in [Protocol Buffer](https://developers.google.com/protocol-buffers/) serializer.

```js
const broker = new ServiceBroker({
    serializer: "ProtoBuf"
});
```

{% note info Dependencies %}
To use this serializer install the `protobufjs` module with `npm install protobufjs --save` command.
{% endnote %}

### Custom serializer

You can also create your custom serializer module. We recommend to copy the source of [JSONSerializer](https://github.com/moleculerjs/moleculer/blob/master/src/serializers/json.js) and implement the `serialize` and `deserialize` methods.

#### Use custom serializer

```js
const { ServiceBroker } = require("moleculer");
const MyAwesomeSerializer = require("./my-serializer");

const broker = new ServiceBroker({
    serializer: new MyAwesomeSerializer()
});
```
