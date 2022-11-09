## **Section 13: Create-Read-Update-Destroy Server Setup**

## Table of Contents

* [**Section 13: Create-Read-Update-Destroy Server Setup**](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#section-13-create-read-update-destroy-server-setup)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)
  * [Ticketing Service Overview](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#ticketing-service-overview)
  * [Project Setup](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#project-setup)
  * [Running the Ticket Service](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#running-the-ticket-service)
  * [Mongo Connection URI](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#mongo-connection-uri)
  * [Quick Auth Update](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#quick-auth-update)
  * [Test-First Approach](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#test-first-approach)
  * [Creating the Router](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#creating-the-router)
  * [Adding Auth Protection](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#adding-auth-protection)
  * [Faking Authentication During Tests](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#faking-authentication-during-tests)
  * [Building a Session](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#building-a-session)
  * [Testing Request Validation](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#testing-request-validation)
  * [Validating Title and Price](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#validating-title-and-price)
  * [Reminder on Mongoose with TypeScript](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#reminder-on-mongoose-with-typescript)
  * [Defining the Ticket Model](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#defining-the-ticket-model)
  * [Creation via Route Handler](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#creation-via-route-handler)
  * [Testing Show Routes](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#testing-show-routes)
  * [Unexpected Failure!](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#unexpected-failure)
  * [What&#39;s that Error?!](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#whats-that-error)
  * [Better Error Logging](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#better-error-logging)
  * [Complete Index Route Implementation](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#complete-index-route-implementation)
  * [Ticket Updating](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#ticket-updating)
  * [Handling Updates](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#handling-updates)
  * [Permission Checking](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#permission-checking)
  * [Final Update Changes](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#final-update-changes)
  * [Manual Testing](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#manual-testing)

### Ticketing Service Overview

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-13/tickets-service.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-13/tickets-service.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-13/tickets-service-mongo-db.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-13/tickets-service-mongo-db.jpg)

Steps

* Create package.json, install deps
* Write Dockerfile
* Create index.ts to run project
* Build image, push to docker hub
* Write k8s file for deployment, service
* Update skaffold.yaml to do file sync for tickets
* Write k8s file for Mongodb deployment, service

Copy from auth service to save time!

* Create package.json, install deps
* Write Dockerfile
* Create index.ts to run project

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Project Setup

* Build image, push to docker hub

```
docker build -t chesterheng/tickets .
docker push chesterheng/tickets
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Running the Ticket Service

* Write k8s file for deployment, service
* Update skaffold.yaml to do file sync for tickets
* Write k8s file for Mongodb deployment, service

```
kubectl get pods
cd section-13/ticketing
skaffold dev
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Mongo Connection URI

```yaml
- name: MONGO_URI
  value: 'mongodb://tickets-mongo-srv:27017/tickets'
```

```ts
try {
  await mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useCreateIndex: true
  });
  console.log('Connected to MongoDb');
} catch (err) {
  console.log(err);
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Quick Auth Update

```yaml
- name: MONGO_URI
  value: 'mongodb://auth-mongo-srv:27017/auth'
```

```ts
try {
  await mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useCreateIndex: true
  });
  console.log('Connected to MongoDb');
} catch (err) {
  console.log(err);
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Test-First Approach

```ts
import request from 'supertest';
import { app } from '../../app';

it('has a route handler listening to /api/tickets for post requests', async () => {});

it('can only be accessed if the user is signed in', async () => {});

it('returns an error if an invalid title is provided', async () => {});

it('returns an error if an invalid price is provided', async () => {});

it('creates a ticket with valid inputs', async () => {});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Creating the Router

```ts
it('has a route handler listening to /api/tickets for post requests', async () => {
  const response = await request(app)
    .post('/api/tickets')
    .send({});

  expect(response.status).not.toEqual(404);
});
```

```ts
import express, { Request, Response } from 'express';

const router = express.Router();

router.post('/api/tickets', (req: Request, res: Response) => {
  res.sendStatus(200);
});

export { router as createTicketRouter };
```

```ts
app.use(createTicketRouter);
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Adding Auth Protection

```ts
it('can only be accessed if the user is signed in', async () => {
  await request(app).post('/api/tickets').send({}).expect(401);
});
```

```ts
app.use(currentUser);
```

```ts
import express, { Request, Response } from 'express';
import { requireAuth } from '@chticketing/common';

const router = express.Router();

router.post('/api/tickets', requireAuth, (req: Request, res: Response) => {
  res.sendStatus(200);
});

export { router as createTicketRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Faking Authentication During Tests

```ts
it('returns a status other than 401 if the user is signed in', async () => {
  const response = await request(app).post('/api/tickets').send({});

  expect(response.status).not.toEqual(401);
});
```

cookie:
express:sess=eyJqd3QiOiJleUpoYkdjaU9pSklVekkxTmlJc0luUjVjQ0k2SWtwWFZDSjkuZXlKcFpDSTZJalZtTVRRd016Y3lPRFUyWkdRek1EQXhPV1U1TkdFd1pTSXNJbVZ0WVdsc0lqb2lkR1Z6ZEVCMFpYTjBMbU52YlNJc0ltbGhkQ0k2TVRVNU5URTBOekV5TW4wLkVicVlVVmY5SjIyUjlOa3k5dVhKdHl3WEh2MVI4ZURuQUlSWFl3RWw4UkEifQ==

[https://www.base64decode.org/](https://www.base64decode.org/)

* Build a JWT payload. { id, email }
* Create the JWT!
* Build Session object. { jwt: MY_JWT }
* Turn that session into JSON
* Take JSON and encode it as base64
* return a string thats the cookie with encoded data

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Building a Session

```ts
it('returns a status other than 401 if the user is signed in', async () => {
  const response = await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({});

  expect(response.status).not.toEqual(401);
});
```

```ts
global.signin = () => {
  // Build a JWT payload. { id, email }
  const payload = {
    id: "5f140372856dd30019e94a0e",
    email: "test@test.com"
  }

  // Create the JWT!
  const token = jwt.sign(payload, process.env.JWT_KEY!);

  // Build Session object. { jwt: MY_JWT }
  const session = { jwt: token }

  // Turn that session into JSON
  const sessionJSON = JSON.stringify(session);

  // Take JSON and encode it as base64
  const base64 = Buffer.from(sessionJSON).toString('base64');

  // return a string thats the cookie with encoded data
  return [`express:sess=${base64}`];
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Testing Request Validation

```ts
it('returns an error if an invalid title is provided', async () => {
  await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      title: '',
      price: 10,
    })
    .expect(400);

  await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      price: 10,
    })
    .expect(400);
});

it('returns an error if an invalid price is provided', async () => {
  await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      title: 'asldkjf',
      price: -10,
    })
    .expect(400);

  await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      title: 'laskdfj',
    })
    .expect(400);
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Validating Title and Price

```ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';
import { requireAuth, validateRequest } from '@chticketing/common';

const router = express.Router();

router.post(
  '/api/tickets',
  requireAuth,
  [
    body('title').not().isEmpty().withMessage('Title is required'),
    body('price')
      .isFloat({ gt: 0 })
      .withMessage('Price must be greater than 0'),
  ],
  validateRequest,
  (req: Request, res: Response) => {
    res.sendStatus(200);
  }
);

export { router as createTicketRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Reminder on Mongoose with TypeScript

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-13/ticket-model.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-13/ticket-model.jpg)

```ts
import mongoose from 'mongoose';

interface TicketAttrs {
  title: string;
  price: number;
  userId: string;
}

interface TicketDoc extends mongoose.Document {
  title: string;
  price: number;
  userId: string;
}

interface TicketModel extends mongoose.Model<TicketDoc> {
  build(attrs: TicketAttrs): TicketDoc;
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Defining the Ticket Model

```ts
const ticketSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true
  },
  price: {
    type: Number,
    required: true
  },
  userId: {
    type: String,
    required: true
  }
}, {
  toJSON: {
    transform(doc, ret) {
      ret.id = ret._id;
      delete ret._id;
    }
  }
});

ticketSchema.statics.build = (attrs: TicketAttrs) => {
  return new Ticket(attrs);
};

const Ticket = mongoose.model<TicketDoc, TicketModel>('Ticket', ticketSchema);

export { Ticket };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Creation via Route Handler

```ts
it('creates a ticket with valid inputs', async () => {
  let tickets = await Ticket.find({});
  expect(tickets.length).toEqual(0);

  const title = 'asldkfj';

  await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      title,
      price: 20,
    })
    .expect(201);

  tickets = await Ticket.find({});
  expect(tickets.length).toEqual(1);
  expect(tickets[0].price).toEqual(20);
  expect(tickets[0].title).toEqual(title);
});
```

```ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';
import { requireAuth, validateRequest } from '@chticketing/common';
import { Ticket } from '../models/ticket';

const router = express.Router();

router.post(
  '/api/tickets',
  requireAuth,
  [
    body('title').not().isEmpty().withMessage('Title is required'),
    body('price')
      .isFloat({ gt: 0 })
      .withMessage('Price must be greater than 0'),
  ],
  validateRequest,
  async (req: Request, res: Response) => {
    const { title, price } = req.body;

    const ticket = Ticket.build({
      title,
      price,
      userId: req.currentUser!.id
    });
    await ticket.save();

    res.sendStatus(201).send(ticket);
  }
);

export { router as createTicketRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Testing Show Routes

```ts
it('returns a 404 if the ticket is not found', async () => {
  await request(app).get('/api/tickets/laskdjfalksfdlkakj').send().expect(404);
});

it('returns the ticket if the ticket is found', async () => {
  const title = 'concert';
  const price = 20;

  const response = await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      title,
      price,
    })
    .expect(201);

  const ticketResponse = await request(app)
    .get(`/api/tickets/${response.body.id}`)
    .send()
    .expect(200);

  expect(ticketResponse.body.title).toEqual(title);
  expect(ticketResponse.body.price).toEqual(price);
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Unexpected Failure!

```ts
import express, { Request, Response } from 'express';
import { NotFoundError } from '@chticketing/common';
import { Ticket } from '../models/ticket';

const router = express.Router();

router.get('/api/tickets/:id', async (req: Request, res: Response) => {
  const ticket = await Ticket.findById(req.params.id);

  if (!ticket) {
    throw new NotFoundError();
  }

  res.send(ticket);
});

export { router as showTicketRouter };
```

```ts
app.use(showTicketRouter);
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### What's that Error?!

```ts
it('returns a 404 if the ticket is not found', async () => {
  const id = new mongoose.Types.ObjectId().toHexString();

  await request(app)
    .get(`/api/tickets/${id}`)
    .send();

  console.log(response.body);
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Better Error Logging

```ts
import { Request, Response, NextFunction } from 'express';
import { CustomError } from '../errors/custom-error';

export const errorHandler = (
  err: Error, 
  req: Request, 
  res: Response, 
  next: NextFunction
) => {
  if(err instanceof CustomError) {
    return res.status(err.statusCode).send({ errors: err.serializeErrors() });
  }

  console.error(err);
  res.status(400).send({
    errors: [{ message: 'Something went wrong' }]
  });
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Complete Index Route Implementation

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-13/tickets-service.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-13/tickets-service.jpg)

```ts
import request from 'supertest';
import { app } from '../../app';

const createTicket = () => {
  return request(app).post('/api/tickets').set('Cookie', global.signin()).send({
    title: 'asldkf',
    price: 20,
  });
};

it('can fetch a list of tickets', async () => {
  await createTicket();
  await createTicket();
  await createTicket();

  const response = await request(app).get('/api/tickets').send().expect(200);

  expect(response.body.length).toEqual(3);
});
```

```ts
import express, { Request, Response } from 'express';
import { Ticket } from '../models/ticket';

const router = express.Router();

router.get('/api/tickets', async (req: Request, res: Response) => {
  const tickets = await Ticket.find({});

  res.send(tickets);
});

export { router as indexTicketRouter };
```

```ts
app.use(indexTicketRouter);
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Ticket Updating

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-13/tickets-service.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-13/tickets-service.jpg)

```ts
it('returns a 404 if the provided id does not exist', async () => {
  const id = new mongoose.Types.ObjectId().toHexString();
  await request(app)
    .put(`/api/tickets/${id}`)
    .set('Cookie', global.signin())
    .send({
      title: 'aslkdfj',
      price: 20,
    })
    .expect(404);
});

it('returns a 401 if the user is not authenticated', async () => {
  const id = new mongoose.Types.ObjectId().toHexString();
  await request(app)
    .put(`/api/tickets/${id}`)
    .send({
      title: 'aslkdfj',
      price: 20,
    })
    .expect(401);
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Handling Updates

```ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';
import {
  validateRequest,
  NotFoundError,
  requireAuth,
  NotAuthorizedError,
} from '@chticketing/common';
import { Ticket } from '../models/ticket';

const router = express.Router();

router.put(
  '/api/tickets/:id',
  requireAuth,
  async (req: Request, res: Response) => {
    const ticket = await Ticket.findById(req.params.id);

    if (!ticket) {
      throw new NotFoundError();
    }

    res.send(ticket);
  }
);

export { router as updateTicketRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Permission Checking

```ts
it('returns a 401 if the user does not own the ticket', async () => {
  const response = await request(app)
    .post('/api/tickets')
    .set('Cookie', global.signin())
    .send({
      title: 'asldkfj',
      price: 20,
    });

  await request(app)
    .put(`/api/tickets/${response.body.id}`)
    .set('Cookie', global.signin())
    .send({
      title: 'alskdjflskjdf',
      price: 1000,
    })
    .expect(401);
});
```

```ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';
import {
  validateRequest,
  NotFoundError,
  requireAuth,
  NotAuthorizedError,
} from '@chticketing/common';
import { Ticket } from '../models/ticket';

const router = express.Router();

router.put(
  '/api/tickets/:id',
  requireAuth,
  async (req: Request, res: Response) => {
    const ticket = await Ticket.findById(req.params.id);

    if (!ticket) {
      throw new NotFoundError();
    }

    if (ticket.userId !== req.currentUser!.id) {
      throw new NotAuthorizedError();
    }

    res.send(ticket);
  }
);

export { router as updateTicketRouter };
```

```ts
const payload = {
  id: new mongoose.Types.ObjectId().toHexString(),
  email: "test@test.com"
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Final Update Changes

```ts
it('returns a 400 if the user provides an invalid title or price', async () => {
  const cookie = global.signin();

  const response = await request(app)
    .post('/api/tickets')
    .set('Cookie', cookie)
    .send({
      title: 'asldkfj',
      price: 20,
    });

  await request(app)
    .put(`/api/tickets/${response.body.id}`)
    .set('Cookie', cookie)
    .send({
      title: '',
      price: 20,
    })
    .expect(400);

  await request(app)
    .put(`/api/tickets/${response.body.id}`)
    .set('Cookie', cookie)
    .send({
      title: 'alskdfjj',
      price: -10,
    })
    .expect(400);
});

it('updates the ticket provided valid inputs', async () => {
  const cookie = global.signin();

  const response = await request(app)
    .post('/api/tickets')
    .set('Cookie', cookie)
    .send({
      title: 'asldkfj',
      price: 20,
    });

  await request(app)
    .put(`/api/tickets/${response.body.id}`)
    .set('Cookie', cookie)
    .send({
      title: 'new title',
      price: 100,
    })
    .expect(200);

  const ticketResponse = await request(app)
    .get(`/api/tickets/${response.body.id}`)
    .send();

  expect(ticketResponse.body.title).toEqual('new title');
  expect(ticketResponse.body.price).toEqual(100);
});
```

```ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';
import {
  validateRequest,
  NotFoundError,
  requireAuth,
  NotAuthorizedError,
} from '@chticketing/common';
import { Ticket } from '../models/ticket';

const router = express.Router();

router.put(
  '/api/tickets/:id',
  requireAuth,
  [
    body('title').not().isEmpty().withMessage('Title is required'),
    body('price').isFloat({ gt: 0 }).withMessage('Price must be provided and must be greater than 0'),
  ],
  validateRequest,
  async (req: Request, res: Response) => {
    const ticket = await Ticket.findById(req.params.id);

    if (!ticket) {
      throw new NotFoundError();
    }

    if (ticket.userId !== req.currentUser!.id) {
      throw new NotAuthorizedError();
    }

    ticket.set({
      title: req.body.title,
      price: req.body.price
    })
    await ticket.save();

    res.send(ticket);
  }
);

export { router as updateTicketRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-13.md#table-of-contents)**

### Manual Testing
