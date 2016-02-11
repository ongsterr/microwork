# Basics

## Replying from subscribers

Microwork provides a simple way to reply from within the subscriber callback without any need for subscriber to be aware of the service.
The can be done using passed `reply` function, like so:
```js
// listen for reply from workers
await service.subscribe('some.topic', (msg, reply) => {
    if (msg === 'ping') {
        reply('reply.topic', 'pong');
    }
});
```

## Unsubscribing

Microwork provides two ways to cancel current subscriptions.

First one is to simply unsubscribe from `topic`. This will remove ALL subscribers that are currently subscribed to the topic.
It can be done like so:
```js
await service.unsubscribe('topic.name');
```

You might want to unsubscribe only a specific consumer.
In this case, you'll need to remember the `consumerTag` you get on subscription and provide it during cancelation.
Note that topic MUST be the same as during subscription.
It can be done using the following code:
```js
// subscribe and save tag
const tag = await service.subscribe('topic.name', () => {});
// ...
// unsubscribe
await service.unsubscribe('topic.name', tag);
```

## Passing queue and consume configs to RabbitMQ

You can pass your custom queue and consume configs to RabbitMQ during subscription.
Simply pass them as third and forth arguments to subscribe function, like so:
```js
await service.subscribe('some.topic', (msg) => {
    // ...
}, queueConfig, consumeConfig);
```

If nothing's passed, default configs will be used.  
Queue config defaults to `{durable: true, autoDelete: true}`.  
Consume config defaults to `{noAck: false}`.

## Acknowledging / rejecting messages

By default Microwork auto-acks all incoming messages for subscribers.
But if needed this can also be done manually.
To do so, provide subscription config, like so:

```js
await service.subscribe('response.topic', (msg, reply, ack, nack) => {
    if (msg === 'hello world!') {
        console.log(msg); // -> "hello world!"
        ack();
    } else {
        nack();
    }
}, queueConfig, consumeConfig, {ack: false});
```