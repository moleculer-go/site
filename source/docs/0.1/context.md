# Context

---

{% note info Go Implementation %}

This documentation has been updated to reflect the Go implementation of Moleculer. All examples are now in Go and show the actual API available in the Go version.
{% endnote %}
When you call an action, the broker creates a `Context` instance which contains all request information and passes it to the action handler as a single argument.

**Available properties & methods of `Context`:**

| Name               | Type            | Description                                                      |
| ------------------ | --------------- | ---------------------------------------------------------------- |
| `ctx.ID()`         | `string`        | Context ID                                                       |
| `ctx.ActionName()` | `string`        | Name of the action being called.                                 |
| `ctx.Caller()`     | `string`        | Caller service name.                                             |
| `ctx.Payload()`    | `Payload`       | Request params wrapped in Payload.                               |
| `ctx.Meta()`       | `Payload`       | Request metadata. _It will be also transferred to nested-calls._ |
| `ctx.Call()`       | `Function`      | You can make a sub-call. Same arguments like `broker.Call`       |
| `ctx.Emit()`       | `Function`      | Emit an event, like `broker.Emit`                                |
| `ctx.Broadcast()`  | `Function`      | Broadcast an event, like `broker.Broadcast`                      |
| `ctx.Logger()`     | `*log.Entry`    | Logger instance for this context.                                |
