# RabbitMQ Stream Python Client

A Python asyncio-based client for [RabbitMQ Streams](https://github.com/rabbitmq/rabbitmq-server/tree/master/deps/rabbitmq_stream)  
_This is a work in progress_

## Install

```bash
pip install rstream
```

## Quick start

Publishing messages:

```python
import asyncio
from rstream import Producer

async def publish():
    async with Producer('localhost', username='guest', password='guest') as producer:
        await producer.create_stream('mystream')

        for i in range(100):
            await producer.publish('mystream', f'msg: {i}'.encode())

asyncio.run(publish())
```

Consuming messages:

```python
import asyncio
from rstream import Consumer

async def consume():
    consumer = Consumer(
        host='localhost',
        port=5552,
        vhost='/',
        username='guest',
        password='guest',
    )

    loop = asyncio.get_event_loop()
    loop.add_signal_handler(signal.SIGINT, lambda: asyncio.create_task(consumer.close()))

    def on_message(msg):
        print('Got message:', msg)

    await consumer.start()
    await consumer.subscribe('mystream', on_message)
    await consumer.run()

asyncio.run(consume())
```

## TODO

- [ ] Documentation
- [ ] Handle `MetadataUpdate` and reconnect to another broker on stream configuration changes
- [ ] AsyncIterator protocol for consumer
- [ ] Add frame size validation
