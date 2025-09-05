# Examples

---

## Built-in Examples

The Moleculer Go repository includes several examples in the `examples/` directory:

### Basic Examples

- **Standalone Math Service** (`examples/standalone/`) - Simple math service with basic actions
- **Math Service with Events** (`examples/math/`) - Math service with event handling and lifecycle methods
- **Redis Transporter** (`examples/redis_transporter/`) - Example using Redis as transporter
- **AMQP Transporter** (`examples/amqp_transporter/`) - Example using AMQP/RabbitMQ as transporter

### Running Examples

```bash
# Simple math service
go run github.com/moleculer-go/moleculer/examples/standalone

# Math service with events
go run github.com/moleculer-go/moleculer/examples/math

# Redis transporter example
go run github.com/moleculer-go/moleculer/examples/redis_transporter

# AMQP transporter example
go run github.com/moleculer-go/moleculer/examples/amqp_transporter
```

## External Examples

-   [WhatsApp App](https://github.com/moleculer-go/example-whatsapp)
-   [Documentation](http://gomicro.services/docs/)
-   [Benchmark repo](https://github.com/moleculer-go/benchmark)
