# Actions

---

The actions are the callable/public methods of the service.
Actions follow the RPC pattern. It has request parameters & returns response, like a HTTP request.

If you have multiple instances of the same services, the broker will load balancing the request among instances. [Read more about balancing](balancing.html).

<div align="center">
![Action balancing diagram](assets/action-balancing.gif)
</div>

## Call services

To call a service, use the `broker.Call()` method or `context.Call()` method. The broker looks for the service (and a node) which has the given action and call it.

### Syntax

```go
res := broker.Call(actionName, params)
```

The `actionName` is a dot-separated string. The first part of it is the service name, while the second part of it represents the action name. So if you have a `posts` service with a `create` action, you can call it as `posts.create`.

The `params` is an `interface{}` that might be serialized in case of remote calls. The `params` are wrapped in a moleculer.Payload which is passed to the action as a part of the [Context](context.html) and as the params argument. A service actions can access it via `ctx.Payload()` or directly in the params argument.

### Usages

**Call without params**

```go
list := <-bkr.Call("user.list", payload.Empty())
fmt.Println("User list: ", list)
```

**Call with params**

```go
user := <-bkr.Call("user.get", map[string]int{"id": 3})
fmt.Println("User: ", user)
```

**Call with error handling**

```go
res := <-bkr.Call("posts.update", map[string]interface{}{"id": 2, "name": nil})
if res.IsError() {
  fmt.Println("Unable to update Post!", res.Error())
} else {
  fmt.Println("Post updated!")
}
```

## moleculer.Context

When you call an action, the broker creates a `moleculer.Context` instance which contains all request information and passes it to the action handler as a single argument.

**Available methods of `Context`:**

| Name             | Description                                                                                                                                       |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MCall(...)`     | Perform multiple calls, label/name each call so the results of each call can be matched to its label/name                                         |
| `Call(...)`      | Perform a single call                                                                                                                             |
| `Emit(...)`      | Emit an event to a single listener                                                                                                                |
| `Broadcast(...)` | Broadcast an event to all listeners                                                                                                               |
| `Logger()`       | Access to the broker logger. The logger contain contextual information, like which nodeID, service name, action name and etc to enhance the logs. |
