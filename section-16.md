## **Section 16: Managing a NATS Client**

## Table of Contents

* [**Section 16: Managing a NATS Client**](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#section-16-managing-a-nats-client)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)
  * [Publishing Ticket Creation](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#publishing-ticket-creation)
  * [More on Publishing](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#more-on-publishing)
  * [NATS Client Singleton](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#nats-client-singleton)
  * [Remember Mongoose?](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#remember-mongoose)
  * [Singleton Implementation](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#singleton-implementation)
  * [Accessing the NATS Client](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#accessing-the-nats-client)
  * [Graceful Shutdown](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#graceful-shutdown)
  * [Successful Listen!](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#successful-listen)
  * [Ticket Update Publishing](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#ticket-update-publishing)
  * [Failed Event Publishing](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#failed-event-publishing)
  * [Handling Publish Failures](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#handling-publish-failures)
  * [Fixing a Few Tests](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#fixing-a-few-tests)
  * [Redirecting Imports](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#redirecting-imports)
  * [Providing a Mock Implementation](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#providing-a-mock-implementation)
  * [Test-Suite Wide Mocks](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#test-suite-wide-mocks)
  * [Ensuring Mock Invocations](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#ensuring-mock-invocations)
  * [NATS Env Variables](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#nats-env-variables)

### Publishing Ticket Creation

```ts
// ticket-created-publisher.ts
import { Publisher, Subjects, TicketCreatedEvent } from '@chticketing/common';

export class TicketCreatedPublisher extends Publisher<TicketCreatedEvent> {
  subject: Subjects.TicketCreated = Subjects.TicketCreated;
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### More on Publishing

```ts
// new.ts
await new TicketCreatedPublisher(client).publish({
  id: ticket.id,
  title: ticket.title,
  price: ticket.price,
  userId: ticket.userId,
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### NATS Client Singleton

Cyclic Dependency Issue

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/cyclic-dependency.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/cyclic-dependency.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/nats-client-singleton.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/nats-client-singleton.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Remember Mongoose?

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/import-mongoose.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/import-mongoose.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/import-nats-client.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/import-nats-client.jpg)

```ts
// nats-wrapper.ts
import nats, { Stan } from 'node-nats-streaming';

class NatsWrapper {}

export const natsWrapper = new NatsWrapper();
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Singleton Implementation

```ts
// nats-wrapper.ts
import nats, { Stan } from 'node-nats-streaming';

class NatsWrapper {
  private _client?: Stan;

  connect(clusterId: string, clientId: string, url: string) {
    this._client = nats.connect(clusterId, clientId, { url });

    return new Promise((resolve, reject) => {
      this._client!.on('connect', () => {
        console.log('Connected to NATS');
        resolve();
      });
      this._client!.on('error', (err) => {
        reject(err);
      });
    });
  }
}

export const natsWrapper = new NatsWrapper();
```

```ts
  try {
    await natsWrapper.connect(
      'ticketing', 
      'dhghad', 
      'http://nats-srv:4222'
    );
  ...
  }
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Accessing the NATS Client

```ts
// nats-wrapper.ts
import nats, { Stan } from 'node-nats-streaming';

class NatsWrapper {
  private _client?: Stan;

  get client() {
    if (!this._client) {
      throw new Error('Cannot access NAT client before connecting')
    }

    return this._client;
  }

  connect(clusterId: string, clientId: string, url: string) {
    this._client = nats.connect(clusterId, clientId, { url });

    return new Promise((resolve, reject) => {
      this.client.on('connect', () => {
        console.log('Connected to NATS');
        resolve();
      });
      this.client.on('error', (err) => {
        reject(err);
      });
    });
  }
}

export const natsWrapper = new NatsWrapper();
```

```ts
// new.ts
import { natsWrapper } from'../nats-wrapper';

  await new TicketCreatedPublisher(natsWrapper.client).publish({
    id: ticket.id,
    title: ticket.title,
    price: ticket.price,
    userId: ticket.userId,
  });
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Graceful Shutdown

```ts
// index.ts
  natsWrapper.client.on('close', () => {
    console.log('NATS connection closed!');
    process.exit();
  });
  process.on('SIGINT', () => natsWrapper.client.close());
  process.on('SIGTERM', () => natsWrapper.client.close());
```

```
kubectl get pods
kubectl delete pod nats-depl-8658cfccf-r9bt8
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Successful Listen!

* Connected to NATS

```
skaffold dev
kubectl get pods
kubectl port-forward nats-depl-8658cfccf-jnb7b 4222:4222
```

* Listener connected to NATS

```
cd section-16/ticketing/nats-test
npm run listen
```

* Create Ticket with Postman
* Check listener receive the ticket created

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Ticket Update Publishing

```ts
import { Publisher, Subjects, TicketUpdatedEvent } from '@chticketing/common';

export class TicketUpdatedPublisher extends Publisher<TicketUpdatedEvent> {
  subject: Subjects.TicketUpdated = Subjects.TicketUpdated;
}
```

```ts
  new TicketUpdatedPublisher(natsWrapper.client).publish({
    id: ticket.id,
    title: ticket.title,
    price: ticket.price,
    userId: ticket.userId
  });
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Failed Event Publishing

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/ticket-create-handler-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/ticket-create-handler-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/ticket-create-handler-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/ticket-create-handler-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/ticket-update-handler.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/ticket-update-handler.jpg)

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/failed-event-publishing-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/failed-event-publishing-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/failed-event-publishing-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/failed-event-publishing-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Handling Publish Failures

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/handle-publish-failure-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/handle-publish-failure-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/handle-publish-failure-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/handle-publish-failure-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/handle-publish-failure-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/handle-publish-failure-3.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/handle-publish-failure-4.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/handle-publish-failure-4.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/handle-publish-failure-5.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/handle-publish-failure-5.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/handle-publish-failure-6.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/handle-publish-failure-6.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Fixing a Few Tests

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/fix-test-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/fix-test-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/fix-test-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/fix-test-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/fix-test-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/fix-test-3.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Redirecting Imports

Mocking (Faking) Imports with Jest

* Find the file that we want to 'fake'
* In the same directory, create a folder called ' **mocks** '
* In that folder, create a file with an identical name to the file we want to fake
* Write a fake implementation
* Tell jest to use that fake file in our test file

```ts
jest.mock('../../nats-wrapper');
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Providing a Mock Implementation

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/mock-implementation-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/mock-implementation-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/mock-implementation-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/mock-implementation-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-16/mock-implementation-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-16/mock-implementation-3.jpg)

```ts
export const natsWrapper = {
  client: {
    publish: (subject: string, data: string, callback: () => void) => {
      callback();
    },
  },
};
```

```ts
// new.test.ts
jest.mock('../../nats-wrapper');
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Test-Suite Wide Mocks

```ts
// setuo.ts
jest.mock('../nats-wrapper');
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### Ensuring Mock Invocations

```ts
export const natsWrapper = {
  client: {
    publish: jest.fn().mockImplementation(
      (subject: string, data: string, callback: () => void) => {
        callback();
    })
  },
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-16.md#table-of-contents)**

### NATS Env Variables

```yaml
  - name: NATS_CLUSTER_ID
    value: 'ticketing'
  - name: NATS_CLIENT_ID
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
  - name: NATS_URL
    value: 'http://nats-srv:4222'
```

```ts
  await natsWrapper.connect(
    process.env.NATS_CLUSTER_ID, 
    process.env.NATS_CLIENT_ID, 
    process.env.NATS_URL
  );
```
