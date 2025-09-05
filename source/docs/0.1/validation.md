# Validation

---

{% note info Go Implementation %}

This documentation has been updated to reflect the Go implementation of Moleculer. All examples are now in Go and show the actual API available in the Go version.
{% endnote %}
Moleculer has a built-in validator module. It uses the [fastest-validator](https://github.com/icebob/fastest-validator) library.

## Built-in validator

```go
import (
    "github.com/moleculer-go/moleculer"
    "github.com/moleculer-go/moleculer/broker"
)

bkr := broker.New(&moleculer.Config{
    Validation: true, // Default is true
})

sayService := moleculer.ServiceSchema{
    Name: "say",
    Actions: []moleculer.Action{
        {
            Name: "hello",
            // Parameters validation can be implemented in the handler
            Handler: func(ctx moleculer.Context, params moleculer.Payload) interface{} {
                name := params.Get("name").String()
                if name == "" {
                    return errors.New("The 'name' field is required!")
                }
                if len(name) < 2 {
                    return errors.New("The 'name' field must be at least 2 characters!")
                }
                return "Hello " + name
            },
        },
    },
}

bkr.Publish(sayService)
bkr.Start()

// This will return an error: "The 'name' field is required!"
result := <-bkr.Call("say.hello", nil)
if result.Error() != nil {
    fmt.Println(result.Error().Error())
}

// This will return an error: "The 'name' field must be at least 2 characters!"
result = <-bkr.Call("say.hello", moleculer.Payload{"name": "A"})
if result.Error() != nil {
    fmt.Println(result.Error().Error())
}

broker
    .call("say.hello", { name: "Walter" })
    .then(console.log)
    .catch(err => console.error(err.message));
// -> "Hello Walter"
```

[Play it on Runkit](https://runkit.com/icebob/moleculer-validation-example)

**Example validation schema**

```js
{
    id: { type: "number", positive: true, integer: true },
    name: { type: "string", min: 3, max: 255 },
    status: "boolean" // short-hand def
}
```

{% note info Documentation %}
You can find more information about validation schema in the [documentation of the library](https://github.com/icebob/fastest-validator#readme)
{% endnote %}

## Custom validator

You can create your custom validator. You should implement `compile` and `validate` methods of `BaseValidator`.

**[Example for Joi](https://gist.github.com/icebob/07024c0ac22589a5496473c2a8a91146)**

```js
// --- JOI VALIDATOR CLASS ---

const BaseValidator = require("moleculer").Validator;
const { ValidationError } = require("moleculer").Errors;

class JoiValidator extends BaseValidator {
    constructor() {
        super();
        this.validator = require("joi");
    }

    compile(schema) {
        return params => this.validate(params, schema);
    }

    validate(params, schema) {
        const res = this.validator.validate(params, schema);
        if (res.error)
            throw new ValidationError(
                res.error.message,
                null,
                res.error.details
            );

        return true;
    }
}

// --- BROKER ---

const { ServiceBroker } = require("moleculer");
const Joi = require("joi");

const broker = new ServiceBroker({
    logger: true,
    validation: true,
    validator: new JoiValidator()
});

// --- SERVICE ---
broker.createService({
    name: "greeter",
    actions: {
        hello: {
            params: Joi.object().keys({
                name: Joi.string()
                    .min(4)
                    .max(30)
                    .required()
            }),
            handler(ctx) {
                return `Hello ${ctx.params.name}`;
            }
        }
    }
});

// --- TEST ---
broker
    .start()
    .then(() =>
        broker.call("greeter.hello").then(res => broker.logger.info(res))
    )
    .catch(err => broker.logger.error(err.message, err.data))

    .then(() =>
        broker
            .call("greeter.hello", { name: 100 })
            .then(res => broker.logger.info(res))
    )
    .catch(err => broker.logger.error(err.message, err.data))

    .then(() =>
        broker
            .call("greeter.hello", { name: "Joe" })
            .then(res => broker.logger.info(res))
    )
    .catch(err => broker.logger.error(err.message, err.data))

    .then(() =>
        broker
            .call("greeter.hello", { name: "John" })
            .then(res => broker.logger.info(res))
    )
    .catch(err => broker.logger.error(err.message, err.data));
```
