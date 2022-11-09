## **Section 18: Understanding Event Flow**

## Table of Contents

* [**Section 18: Understanding Event Flow**](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#section-18-understanding-event-flow)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#table-of-contents)
  * [Orders Service Events](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#orders-service-events)
  * [Creating the Events](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#creating-the-events)
  * [Implementing the Publishers](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#implementing-the-publishers)
  * [Publishing the Order Creation](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#publishing-the-order-creation)
  * [Publishing Order Cancellation](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#publishing-order-cancellation)
  * [Testing Event Publishing](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#testing-event-publishing)

### Orders Service Events

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-18/events.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-18/events.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-18/order-created.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-18/order-created.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-18/order-cancelled.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-18/order-cancelled.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#table-of-contents)**

### Creating the Events

```ts
import { Subjects } from './subjects';
import { OrderStatus } from './types/order-status';

export interface OrderCreatedEvent {
  subject: Subjects.OrderCreated;
  data: {
    id: string;
    status: OrderStatus;
    userId: string;
    expiresAt: string;
    ticket: {
      id: string;
      price: number;
    };
  };
}
```

```ts
import { Subjects } from './subjects';

export interface OrderCancelledEvent {
  subject: Subjects.OrderCancelled;
  data: {
    id: string;
    ticket: {
      id: string;
    };
  };
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#table-of-contents)**

### Implementing the Publishers

```ts
import { Publisher, OrderCreatedEvent, Subjects } from '@chticketing/common';

export class OrderCreatedPublisher extends Publisher<OrderCreatedEvent> {
  subject: Subjects.OrderCreated = Subjects.OrderCreated;
}
```

```ts
import { Subjects, Publisher, OrderCancelledEvent } from '@chticketing/common';

export class OrderCancelledPublisher extends Publisher<OrderCancelledEvent> {
  subject: Subjects.OrderCancelled = Subjects.OrderCancelled;
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#table-of-contents)**

### Publishing the Order Creation

```ts
  // Publish an event saying that an order was created
  new OrderCreatedPublisher(natsWrapper.client).publish({
    id: order.id,
    status: order.status,
    userId: order.userId,
    expiresAt: order.expiresAt.toISOString(),
    ticket: {
      id: ticket.id,
      price: ticket.price,
    },
  });
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#table-of-contents)**

### Publishing Order Cancellation

```ts
  // publishing an event saying this was cancelled!
  new OrderCancelledPublisher(natsWrapper.client).publish({
    id: order.id,
    ticket: {
      id: order.ticket.id,
    },
  });
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-18.md#table-of-contents)**

### Testing Event Publishing
