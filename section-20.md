## **Section 20: Worker Services**

## Table of Contents

* [**Section 20: Worker Services**](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#section-20-worker-services)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)
  * [The Expiration Service](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#the-expiration-service)
  * [Expiration Options](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#expiration-options)
  * [Initial Setup](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#initial-setup)
  * [A Touch of Kubernetes Setup](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#a-touch-of-kubernetes-setup)
  * [File Sync Setup](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#file-sync-setup)
  * [Listener Creation](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#listener-creation)
  * [What&#39;s Bull All About?](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#whats-bull-all-about)
  * [Creating a Queue](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#creating-a-queue)
  * [Queueing a Job on Event Arrival](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#queueing-a-job-on-event-arrival)
  * [Testing Job Processing](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#testing-job-processing)
  * [Delaying Job Processing](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#delaying-job-processing)
  * [Defining the Expiration Complete Event](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#defining-the-expiration-complete-event)
  * [Publishing an Event on Job Processing](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#publishing-an-event-on-job-processing)
  * [Handling an Expiration Event](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#handling-an-expiration-event)
  * [Emitting the Order Cancelled Event](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#emitting-the-order-cancelled-event)
  * [Testing the Expiration Complete Listener](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#testing-the-expiration-complete-listener)
  * [A Touch More Testing](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#a-touch-more-testing)
  * [Listening for Expiration](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#listening-for-expiration)
  * [Don&#39;t Cancel Completed Orders!](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#dont-cancel-completed-orders)

### The Expiration Service

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/expiration-service-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/expiration-service-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/expiration-service-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/expiration-service-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Expiration Options

* Option 1: Use timer
* Option 2: Rely on NATS redelivery mechanism
* Option 3: Scheduled Event (Not supported by NATS)
* Option 4: Use Bull JS

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/expiration-options.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/expiration-options.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/option-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/option-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/option-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/option-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/option-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/option-3.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/option-4.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/option-4.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Initial Setup

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/setup.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/setup.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### A Touch of Kubernetes Setup

```
docker build -t chesterheng/expiration .
docker push chesterheng/expiration
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: expiration-redis-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: expiration-redis
  template:
    metadata:
      labels:
        app: expiration-redis
    spec:
      containers:
        - name: expiration-redis
          image: redis
---
apiVersion: v1
kind: Service
metadata:
  name: expiration-redis-srv
spec:
  selector:
    app: expiration-redis
  ports:
    - name: db
      protocol: TCP
      port: 6379
      targetPort: 6379
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: expiration-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: expiration
  template:
    metadata:
      labels:
        app: expiration
    spec:
      containers:
        - name: expiration
          image: chesterheng/expiration
          env:
            - name: NATS_CLIENT_ID
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: NATS_URL
              value: 'http://nats-srv:4222'
            - name: NATS_CLUSTER_ID
              value: ticketing
            - name: REDIS_HOST
              value: expiration-redis-srv
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### File Sync Setup

```yaml
  - image: chesterheng/expiration
    context: expiration
    docker:
      dockerfile: Dockerfile
    sync:
      manual:
        - src: 'src/**/*.ts'
          dest: .
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Listener Creation

```ts
export class OrderCreatedListener extends Listener<OrderCreatedEvent> {
  subject: Subjects.OrderCreated = Subjects.OrderCreated;
  queueGroupName = queueGroupName;

  async onMessage(data: OrderCreatedEvent['data'], msg: Message) {}
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### What's Bull All About?

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/bull-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/bull-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/bull-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/bull-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/bull-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/bull-3.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Creating a Queue

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/bull-4.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/bull-4.jpg)

```ts
import Queue from 'bull';

interface Payload {
  orderId: string;
}

const expirationQueue = new Queue<Payload>('order:expiration', {
  redis: {
    host: process.env.REDIS_HOST,
  },
});

expirationQueue.process(async (job) => {
  console.log(
    'I want to publish an expiration:complete event for orderId',
    job.data.orderId
  );
});

export { expirationQueue };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Queueing a Job on Event Arrival

```ts
  async onMessage(data: OrderCreatedEvent['data'], msg: Message) {
    await expirationQueue.add({
      orderId: data.id,
    });

    msg.ack();
  }
```

```ts
new OrderCreatedListener(natsWrapper.client).listen()
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Testing Job Processing

* Signup
* Create Ticket
* Create Order

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Delaying Job Processing

```ts
  async onMessage(data: OrderCreatedEvent['data'], msg: Message) {
    const delay = new Date(data.expiresAt).getTime() - new Date().getTime();
    console.log('Waiting this many milliseconds to process the job:', delay);

    await expirationQueue.add(
      {
        orderId: data.id,
      },
      {
        delay,
      }
    );

    msg.ack();
  }
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Defining the Expiration Complete Event

```ts
  ExpirationComplete = 'expiration:complete',
```

```ts
import { Subjects } from './subjects';

export interface ExpirationCompleteEvent {
  subject: Subjects.ExpirationComplete;
  data: {
    orderId: string;
  };
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Publishing an Event on Job Processing

```ts
import {
  Subjects,
  Publisher,
  ExpirationCompleteEvent,
} from '@chticketing/common';

export class ExpirationCompletePublisher extends Publisher<ExpirationCompleteEvent> {
  subject: Subjects.ExpirationComplete = Subjects.ExpirationComplete;
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Handling an Expiration Event

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/expiration-complete.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/expiration-complete.jpg)

```ts
export class ExpirationCompleteListener extends Listener<ExpirationCompleteEvent> {
  queueGroupName = queueGroupName;
  subject: Subjects.ExpirationComplete = Subjects.ExpirationComplete;

  async onMessage(data: ExpirationCompleteEvent['data'], msg: Message) {
    const order = await Order.findById(data.orderId);

    if (!order) {
      throw new Error('Order not found');
    }

    order.set({
      status: OrderStatus.Cancelled,
      ticket: null,
    });
  }
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Emitting the Order Cancelled Event

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-20/order-cancelled.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-20/order-cancelled.jpg)

```ts
  await new OrderCancelledPublisher(this.client).publish({
    id: order.id,
    version: order.version,
    ticket: {
      id: order.ticket.id,
    },
  });
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Testing the Expiration Complete Listener

```ts
const setup = async () => {
  const listener = new ExpirationCompleteListener(natsWrapper.client);

  const ticket = Ticket.build({
    id: mongoose.Types.ObjectId().toHexString(),
    title: 'concert',
    price: 20,
  });
  await ticket.save();
  const order = Order.build({
    status: OrderStatus.Created,
    userId: 'alskdfj',
    expiresAt: new Date(),
    ticket,
  });
  await order.save();

  const data: ExpirationCompleteEvent['data'] = {
    orderId: order.id,
  };

  // @ts-ignore
  const msg: Message = {
    ack: jest.fn(),
  };

  return { listener, order, ticket, data, msg };
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### A Touch More Testing

```ts
it('updates the order status to cancelled', async () => {
  const { listener, order, data, msg } = await setup();

  await listener.onMessage(data, msg);

  const updatedOrder = await Order.findById(order.id);
  expect(updatedOrder!.status).toEqual(OrderStatus.Cancelled);
});

it('emit an OrderCancelled event', async () => {
  const { listener, order, data, msg } = await setup();

  await listener.onMessage(data, msg);

  expect(natsWrapper.client.publish).toHaveBeenCalled();

  const eventData = JSON.parse(
    (natsWrapper.client.publish as jest.Mock).mock.calls[0][1]
  );
  expect(eventData.id).toEqual(order.id);
});

it('ack the message', async () => {
  const { listener, data, msg } = await setup();

  await listener.onMessage(data, msg);

  expect(msg.ack).toHaveBeenCalled();
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Listening for Expiration

```ts
new ExpirationCompleteListener(natsWrapper.client).listen();
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-20.md#table-of-contents)**

### Don't Cancel Completed Orders!

```ts
  if (order.status === OrderStatus.Complete) {
    return msg.ack();
  }
```
