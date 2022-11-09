## **Section 15: Connecting to NATS in a Node JS World**

## Table of Contents

* [**Section 15: Connecting to NATS in a Node JS World**](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#section-15-connecting-to-nats-in-a-node-js-world)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)
  * [Reusable NATS Listeners](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#reusable-nats-listeners)
  * [The Listener Abstract Class](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#the-listener-abstract-class)
  * [Extending the Listener](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#extending-the-listener)
  * [Quick Refactor](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#quick-refactor)
  * [Leveraging TypeScript for Listener Validation](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#leveraging-typescript-for-listener-validation)
  * [Subjects Enum](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#subjects-enum)
  * [Custom Event Interface](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#custom-event-interface)
  * [Enforcing Listener Subjects](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#enforcing-listener-subjects)
  * [Quick Note: &#39;readonly&#39; in Typescript](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#quick-note-readonly-in-typescript)
  * [Enforcing Data Types](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#enforcing-data-types)
  * [Where Does this Get Used?](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#where-does-this-get-used)
  * [Custom Publisher](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#custom-publisher)
  * [Using the Custom Publisher](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#using-the-custom-publisher)
  * [Awaiting Event Publication](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#awaiting-event-publication)
  * [Common Event Definitions Summary](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#common-event-definitions-summary)
  * [Updating the Common Module](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#updating-the-common-module)
  * [Restarting NATS](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#restarting-nats)

### Reusable NATS Listeners

* Wow, this is a lot of boilerplate to publish/receive a message!
* Let's try to refactor this to make it much easier to publish/receive
* We'll write out an initial implementation in this test project, then move it to our common module

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/class-listener-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/class-listener-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/class-listener-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/class-listener-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### The Listener Abstract Class

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/class-listener-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/class-listener-1.jpg)

```ts
abstract class Listener {
  abstract subject: string;
  abstract queueGroupName: string;
  abstract onMessage(data: any, msg: Message): void;
  private client: Stan;
  protected ackWait = 5 * 1000;

  constructor(client: Stan) {
    this.client = client;
  }

  subscriptionOptions() {
    return this.client
    .subscriptionOptions()
    .setDeliverAllAvailable()
    .setManualAckMode(true)
    .setAckWait(this.ackWait)
    .setDurableName(this.queueGroupName);
  }

  listen() {
    const subscription = this.client.subscribe(
      this.subject,
      this.queueGroupName,
      this.subscriptionOptions()
    )

    subscription.on('message', (msg: Message) => {
      console.log(`Message received: ${this.subject} / ${this.queueGroupName}`);

      const parsedData = this.parseMessage(msg);
      this.onMessage(parsedData, msg);
    })
  }

  parseMessage(msg: Message) {
    const data = msg.getData();
    return typeof data === 'string'
      ? JSON.parse(data)
      : JSON.parse(data.toString('utf8'))
  }
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Extending the Listener

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/class-listener-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/class-listener-2.jpg)

```ts
class TicketCreatedListener extends Listener {
  subject = 'ticket:created';
  queueGroupName = 'payments-service';

  onMessage(data: any, msg: Message) {
    console.log('Event data!', data);

    msg.ack();
  }
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Quick Refactor

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/class-listener-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/class-listener-3.jpg)

```ts
// listener.ts
import nats from 'node-nats-streaming';
import { randomBytes } from 'crypto';
import { TicketCreatedListener } from './events/ticket-created-listener';

console.clear();

const stan = nats.connect('ticketing', randomBytes(4).toString('hex'), {
  url: 'http://localhost:4222',
});

stan.on('connect', () => {
  console.log('Listener connected to NATS');

  stan.on('close', () => {
    console.log('NATS connection closed!');
    process.exit();
  });

  new TicketCreatedListener(stan).listen();
});

process.on('SIGINT', () => stan.close());
process.on('SIGTERM', () => stan.close());
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Leveraging TypeScript for Listener Validation

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/subject-name-event-data.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/subject-name-event-data.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/ticket-created-listener.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/ticket-created-listener.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/mismatch.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/mismatch.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Subjects Enum

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/ticket-created-listener.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/ticket-created-listener.jpg)

```ts
export enum Subjects {
  TicketCreated = 'ticket:created',
  OrderUpdated = 'order:updated',
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Custom Event Interface

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/ticket-created-listener.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/ticket-created-listener.jpg)

```ts
// ticket-created-event.ts
import { Subjects } from "./subjects";

export interface TicketCreatedEvent {
  subject: Subjects.TicketCreated;
  data: {
    id: string;
    title: string;
    price: number;
  };
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Enforcing Listener Subjects

```ts
// base-listener.ts
import { Subjects } from './subjects';

interface Event {
  subject: Subjects;
  data: any;
}

export abstract class Listener<T extends Event> {
  abstract subject: T['subject'];
  abstract onMessage(data: T['data'], msg: Message): void;
}
```

```ts
// ticket-created-listener.ts
import { TicketCreatedEvent } from './ticket-created-event'
import { Subjects } from './subjects';

export class TicketCreatedListener extends Listener<TicketCreatedEvent> {
  subject: Subjects.TicketCreated = Subjects.TicketCreated;
  ...
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Quick Note: 'readonly' in Typescript

```ts
export class TicketCreatedListener extends Listener<TicketCreatedEvent> {
  readonly subject = Subjects.TicketCreated;
 
  // ...everything else
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Enforcing Data Types

```ts
// ticket-created-listener.ts
export class TicketCreatedListener extends Listener<TicketCreatedEvent> {

  onMessage(data: TicketCreatedEvent['data'], msg: Message) {
    console.log('Event data!', data);

    console.log(data.id);
    console.log(data.title);
    console.log(data.price);

    msg.ack();
  }
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Where Does this Get Used?

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/common-module.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/common-module.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Custom Publisher

```ts
// base-publisher.ts
import { Stan } from 'node-nats-streaming';
import { Subjects } from './subjects';

interface Event {
  subject: Subjects;
  data: any;
}

export abstract class Publisher<T extends Event> {
  abstract subject: T['subject'];
  private client: Stan;

  constructor(client: Stan) {
    this.client = client;
  }

  publish(data: T['data']) {
    this.client.publish(this.subject, JSON.stringify(data), () => {
      console.log('Event published.')
    })
  }
}
```

```ts
// ticket-created-publisher.ts
import { Publisher } from './base-publisher';
import { TicketCreatedEvent } from './ticket-created-event'
import { Subjects } from './subjects';

export class TicketCreatedPublisher extends Publisher<TicketCreatedEvent> {
  readonly subject = Subjects.TicketCreated;
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Using the Custom Publisher

```ts
// publisher.ts
stan.on('connect', () => {
  console.log('Publisher connected to NATS');

  const publisher = new TicketCreatedPublisher(stan);
  publisher.publish({
    id: '123',
    title: 'concert',
    price: 20
  });
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Awaiting Event Publication

```ts
// base-publisher.ts
  publish(data: T['data']): Promise<void> {
    return new Promise((resolve, reject) => {
      this.client.publish(this.subject, JSON.stringify(data), (err) => {
        if (err) {
          return reject(err);
        }
        console.log('Event published to subject', this.subject);
        resolve();
      });
    });
  }
```

```ts
// publisher.ts
stan.on('connect', async () => {
  console.log('Publisher connected to NATS');

  const publisher = new TicketCreatedPublisher(stan);
  try {
    await publisher.publish({
      id: '123',
      title: 'concert',
      price: 20
    });
  }
  catch (err) {
    console.error(err);
  }
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Common Event Definitions Summary

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/common-event.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/common-event.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/common-module-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/common-module-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/common-module-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/common-module-3.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-15/cross-language-support.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-15/cross-language-support.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Updating the Common Module

* base-listener.ts
* base-publisher.ts
* subjects.ts
* ticket-created-event.ts
* ticket-updated-event.ts

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-15.md#table-of-contents)**

### Restarting NATS

```
kubectl get pods
kubectl delete pod nats-depl-786b8cff8d-xd4tn
```
