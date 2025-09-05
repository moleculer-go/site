# Usage

---

# Install Moleculer Go

Moleculer can be installed with go get or go modules

```bash
$ go get -u github.com/moleculer-go/moleculer

# Or with go modules
go mod init my-project
go get github.com/moleculer-go/moleculer
```

# Create your first microservice

This example shows how to create a small `math` service to add two numbers.

```go
package main

import (
 "fmt"

 "github.com/moleculer-go/moleculer"
 "github.com/moleculer-go/moleculer/broker"
 "github.com/moleculer-go/moleculer/payload"
)

var mathService = moleculer.ServiceSchema{
 Name: "math",
 Actions: []moleculer.Action{
  {
   Name: "add",
   Handler: func(ctx moleculer.Context, params moleculer.Payload) interface{} {
    return params.Get("a").Int() + params.Get("b").Int()
   },
  },
 },
}

func main() {
 var bkr = broker.New(&moleculer.Config{LogLevel: "error"})
 bkr.Publish(mathService)
 bkr.Start()
 result := <-bkr.Call("math.add", payload.New(map[string]int{
  "a": 10,
  "b": 130,
 }))
 fmt.Println("result: ", result.Int()) //$ result: 140
 bkr.Stop()
}
```

Try it!

```bash
$ go run github.com/moleculer-go/moleculer/examples/standalone
```

# Create a Moleculer Go project

<img src="assets/under_construction.png" width=150/>

Use the [Moleculer CLI tool](moleculer-cli.html) to create a new Moleculer-based microservices project.

1. Create a new project (named `first-demo`)

    ```bash
    $ moleculer init project first-demo
    ```

    > Press Y to all questions

2. Open project folder

    ```bash
    $ cd first-demo
    ```

3. Start project
    ```bash
    $ go run .
    ```
4. Open the [http://localhost:3000/](http://localhost:3000/) link in your browser. It shows a start page which contains two links to call the `greeter` service via [API gateway](https://github.com/moleculer-go/gateway).

{% note info Congratulations! %}
You have just created your first Moleculer-based microservices project! The next step is to check our [examples](examples.html) or [demo projects](https://github.com/moleculer-go/moleculer-examples).
{% endnote %}
