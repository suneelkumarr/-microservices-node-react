## **Section 09: Authentication Strategies and Options**

## Table of Contents

* [**Section 09: Authentication Strategies and Options**](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#section-09-authentication-strategies-and-options)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)
  * [Fundamental Authentication Strategies](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#fundamental-authentication-strategies)
  * [Huge Issues with Authentication Strategies](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#huge-issues-with-authentication-strategies)
  * [So Which Option?](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#so-which-option)
  * [Solving Issues with Option #2](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#solving-issues-with-option-2)
  * [Reminder on Cookies vs JWT&#39;s](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#reminder-on-cookies-vs-jwts)
  * [Microservices Auth Requirements](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#microservices-auth-requirements)
  * [Issues with JWT&#39;s and Server Side Rendering](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#issues-with-jwts-and-server-side-rendering)
  * [Cookies and Encryption](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#cookies-and-encryption)
  * [Adding Session Support](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#adding-session-support)
  * [Note on Cookie-Session - Do Not Skip](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#note-on-cookie-session---do-not-skip)
  * [Generating a JWT](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#generating-a-jwt)
  * [JWT Signing Keys](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#jwt-signing-keys)
  * [Securely Storing Secrets with Kubernetes](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#securely-storing-secrets-with-kubernetes)
  * [Creating and Accessing Secrets](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#creating-and-accessing-secrets)
  * [Accessing Env Variables in a Pod](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#accessing-env-variables-in-a-pod)
  * [Common Response Properties](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#common-response-properties)
  * [Formatting JSON Properties](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#formatting-json-properties)
  * [The Signin Flow](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#the-signin-flow)
  * [Common Request Validation Middleware](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#common-request-validation-middleware)
  * [Sign In Logic](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#sign-in-logic)
  * [Current User Handler](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#current-user-handler)
  * [Returning the Current User](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#returning-the-current-user)
  * [Signing Out](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#signing-out)
  * [Creating a Current User Middleware](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#creating-a-current-user-middleware)
  * [Augmenting Type Definitions](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#augmenting-type-definitions)
  * [Requiring Auth for Route Access](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#requiring-auth-for-route-access)

### Fundamental Authentication Strategies

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/auth-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/auth-2.jpg)

* User auth with microservices is an unsolved problem
* There are many ways to do it, and no one way is "right"
* I am going to outline a couple solutions then propose a solution that works, but still has downsides

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/order-service.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/order-service.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/option-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/option-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/option-1-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/option-1-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/option-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/option-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Huge Issues with Authentication Strategies

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/user-signin.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/user-signin.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/option-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/option-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/admin-ban-user.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/admin-ban-user.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/ban-user-can-buy-ticket.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/ban-user-can-buy-ticket.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### So Which Option?

Fundamental Option #1

* Individual services rely on the auth service
* Changes to auth state are immediately reflected
* Auth service goes down?  Entire app is broken

Fundamental Option #2

* Individual services know how to authenticate a user
* Auth service is down? Who cares!
* Some user got banned? Darn, I just gave them the keys to my car 5 minutes ago...

We are going with Option #2 to stick with the idea of independent services

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Solving Issues with Option #2

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/user-signin-time-constraint.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/user-signin-time-constraint.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/refresh-token-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/refresh-token-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/refresh-token-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/refresh-token-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/cache-banned-users.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/cache-banned-users.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Reminder on Cookies vs JWT's

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/option-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/option-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/cookie.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/cookie.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/jwt.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/jwt.jpg)

| Cookies                                           | JWT's                                  |
| ------------------------------------------------- | -------------------------------------- |
| Transport mechanism                               | Authentication/Authorization mechanism |
| Moves any kind of data between browser and server | Stores any data we want                |
| Automatically managed by the browser              | We have to manage it manually          |

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Microservices Auth Requirements

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/login-user-details.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/login-user-details.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/admin-ban-user.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/admin-ban-user.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/login-expire.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/login-expire.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/auth-mechanism.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/auth-mechanism.jpg)

Requirements for Our Auth Mechanism -> JWT

* Must be able to tell us details about a user
* Must be able to handle authorization info
* Must have a built-in, tamper-resistant way to expire or - invalidate itself
* Must be easily understood between different languages
* Must not require some kind of backing data store on the server

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/client-jwt-server.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/client-jwt-server.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Issues with JWT's and Server Side Rendering

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/requests-responses.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/requests-responses.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/ssr-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/ssr-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/ssr-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/ssr-2.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/ssr-3.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/ssr-3.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/jwt-in-cookie.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/jwt-in-cookie.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Cookies and Encryption

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/signup.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/signup.jpg)

Requirements for Our Auth Mechanism

* Must be able to tell us details about a user
* Must be able to handle authorization info
* Must have a built-in, tamper-resistant way to expire or - invalidate itself
* Must be easily understood between different languages
  * Cookie handling across languages is usually an issue when we encrypt the data in the cookie
  * We will not encrypt the cookie contents.
  * Remember, JWT's are tamper resistant
  * You can encrypt the cookie contents if this is a big deal to you
* Must not require some kind of backing data store on the server

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Adding Session Support

[cookie-session](https://github.com/expressjs/cookie-session)

```ts
// index.ts
app.set('trust proxy', true);
app.use(json());
app.use(
  cookieSession({
    signed: false,
    secure: true
  })
);
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Note on Cookie-Session - Do Not Skip

The latest version of the @types/cookie-session package
has a bug in it! Yes, a real bug - the type defs written out incorrectly
 describes the session object.

To fix this, we'll use a slightly earlier version of the package until this bug gets fixed.

Run the following inside the auth project:

```
npm uninstall @types/cookie-session
npm install --save-exact @types/cookie-session@2.0.39
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Generating a JWT

[jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)

```ts
// signup.ts
// Generate JWT
const userJwt = jwt.sign(
  {
    id: user.id,
    email: user.email
  },
  'asdf'
);

// Store it on session object
req.session = {
  jwt: userJwt
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### JWT Signing Keys

[BASE64 Decode](https://www.base64decode.org/)
[JWT](https://jwt.io/)

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/sign-jwt.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/sign-jwt.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/jwt-signing-key.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/jwt-signing-key.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Securely Storing Secrets with Kubernetes

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/secret-object-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/secret-object-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/secret-object-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/secret-object-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Creating and Accessing Secrets

Creating a Secret

```
kubectl create secret generic jwt-secret --from-literal=JWT_KEY=asdf
kubectl get secrets
kubectl describe secret jwt-secret
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Accessing Env Variables in a Pod

```ts
if(!process.env.JWT_KEY) {
  throw new Error('JWT_KEY must be defined');
}
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Common Response Properties

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/different-db-issue.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/different-db-issue.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/different-db-solution.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/different-db-solution.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Formatting JSON Properties

```ts
const person = { name: 'alex' };
JSON.stringify(person)

const personTwo = { 
  name: 'alex', 
  toJSON() { return 1; } 
};
JSON.stringify(personTwo)
```

```ts
const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: true
  },
  password: {
    type: String,
    required: true
  }
}, {
  toJSON: {
    transform(doc, ret) {
      ret.id = ret._id;
      delete ret._id;
      delete ret.password;
      delete ret.__v;
    }
  }
});
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### The Signin Flow

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/signin-flow.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/signin-flow.jpg)

```ts
import express, { Request, Response } from 'express';
import { body, validationResult } from 'express-validator';

import { RequestValidationError } from '../errors/request-validation-error';

const router = express.Router();

router.post(
  '/api/users/signin',
  [
    body('email')
      .isEmail()
      .withMessage('Email must be valid'),
    body('password')
      .trim()
      .notEmpty()
      .withMessage('You must supply a password')
  ],
  (req: Request, res: Response) => {
    const errors = validationResult(req);

    if (!errors.isEmpty()) {
      throw new RequestValidationError(errors.array());
    }
  }
);

export { router as signinRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Common Request Validation Middleware

```ts
// validate-request.ts
import { Request, Response, NextFunction } from 'express';
import { validationResult } from 'express-validator';
import { RequestValidationError } from '../errors/request-validation-error';

export const validateRequest = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const errors = validationResult(req);

  if (!errors.isEmpty()) {
    throw new RequestValidationError(errors.array());
  }

  next();
};
```

```ts
// signin.ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';

import { validateRequest } from '../middleware/validate-request';

const router = express.Router();

router.post(
  '/api/users/signin',
  [
    body('email')
      .isEmail()
      .withMessage('Email must be valid'),
    body('password')
      .trim()
      .notEmpty()
      .withMessage('You must supply a password')
  ],
  validateRequest,
  (req: Request, res: Response) => {

  }
);

export { router as signinRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Sign In Logic

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/signin-flow.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/signin-flow.jpg)

```ts
import express, { Request, Response } from 'express';
import { body } from 'express-validator';
import jwt from 'jsonwebtoken';

import { Password } from '../services/password';
import { User } from '../models/user';
import { validateRequest } from '../middlewares/validate-request';
import { BadRequestError } from '../errors/bad-request-error';

const router = express.Router();

router.post(
  '/api/users/signin',
  [
    body('email')
      .isEmail()
      .withMessage('Email must be valid'),
    body('password')
      .trim()
      .notEmpty()
      .withMessage('You must supply a password')
  ],
  validateRequest,
  async (req: Request, res: Response) => {
    const { email, password } = req.body;

    const existingUser = await User.findOne({ email });
    if (!existingUser) {
      throw new BadRequestError('Invalid credentials');
    }

    const passwordsMatch = await Password.compare(
      existingUser.password,
      password
    );
    if (!passwordsMatch) {
      throw new BadRequestError('Invalid Credentials');
    }

    // Generate JWT
    const userJwt = jwt.sign(
      {
        id: existingUser.id,
        email: existingUser.email
      },
      process.env.JWT_KEY!
    );

    // Store it on session object
    req.session = {
      jwt: userJwt
    };

    res.status(200).send(existingUser);
  }
);

export { router as signinRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Current User Handler

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-09/current-user.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-09/current-user.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Returning the Current User

```ts
import express from 'express';
import jwt from 'jsonwebtoken';

const router = express.Router();

router.get('/api/users/currentuser', (req, res) => {
  if (!req.session?.jwt) {
    return res.send({ currentUser: null });
  }

  try {
    const payload = jwt.verify(
      req.session.jwt, 
      process.env.JWT_KEY!
    );
    res.send({ currentUser: payload });
  } catch (err) {
    res.send({ currentUser: null });
  }
});

export { router as currentUserRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Signing Out

```ts
import express from 'express';

const router = express.Router();

router.post('/api/users/signout', (req, res) => {
  req.session = null;

  res.send({});
});

export { router as signoutRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Creating a Current User Middleware

```ts
// current-user.ts
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

export const currentUser = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (!req.session?.jwt) {
    return next();
  }

  try {
    const payload = jwt.verify(req.session.jwt, process.env.JWT_KEY!);
    req.currentUser = payload;
  } catch (err) {}

  next();
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Augmenting Type Definitions

```ts
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

interface UserPayload {
  id: string;
  email: string;
}

declare global {
  namespace Express {
    interface Request {
      currentUser?: UserPayload;
    }
  }
}

export const currentUser = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (!req.session?.jwt) {
    return next();
  }

  try {
    const payload = jwt.verify(
      req.session.jwt,
      process.env.JWT_KEY!
    ) as UserPayload;
    req.currentUser = payload;
  } catch (err) {}

  next();
};
```

```ts
import express from 'express';
import jwt from 'jsonwebtoken';

import { currentUser } from '../middlewares/current-user';

const router = express.Router();

router.get('/api/users/currentuser', currentUser, (req, res) => {
  res.send({ currentUser: req.currentUser || null });
});

export { router as currentUserRouter };
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-09.md#table-of-contents)**

### Requiring Auth for Route Access

```ts
import { CustomError } from './custom-error';

export class NotAuthorizedError extends CustomError {
  statusCode = 401;

  constructor() {
    super('Not Authorized');

    Object.setPrototypeOf(this, NotAuthorizedError.prototype);
  }

  serializeErrors() {
    return [{ message: 'Not authorized' }];
  }
}
```

```ts
import { Request, Response, NextFunction } from 'express';
import { NotAuthorizedError } from '../errors/not-authorized-error';

export const requireAuth = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (!req.currentUser) {
    throw new NotAuthorizedError();
  }

  next();
};
```

```ts
import express from 'express';
import jwt from 'jsonwebtoken';

import { currentUser } from '../middlewares/current-user';
import { requireAuth } from '../middlewares/require-auth';

const router = express.Router();

router.get(
  '/api/users/currentuser', 
  currentUser, 
  requireAuth, 
  (req, res) => {
    res.send({ currentUser: req.currentUser || null });
  });

export { router as currentUserRouter };
```
