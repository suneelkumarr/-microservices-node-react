## **Section 03: Running Services with Docker**

## Table of Contents

* [**Section 03: Running Services with Docker**](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#section-03-running-services-with-docker)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#table-of-contents)
  * [Deployment Issues](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#deployment-issues)
  * [Why Docker?](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#why-docker)
  * [Why Kubernetes?](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#why-kubernetes)
  * [Dockerizing the Posts Service](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#dockerizing-the-posts-service)
  * [Review Some Basic Commands](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#review-some-basic-commands)
  * [Dockering Other Services](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#dockering-other-services)

### Deployment Issues

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-03/01-your-computer.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-03/01-your-computer.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-03/02-virtual-machine.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-03/02-virtual-machine.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-03/03-scale-virtual-machine.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-03/03-scale-virtual-machine.jpg)

```js
// event-bus/index.js
app.post('/events', (req, res) => {
  const event = req.body;
  events.push(event);

  axios.post('http://localhost:4000/events', event);
  axios.post('http://localhost:4001/events', event);
  axios.post('http://localhost:4002/events', event);
  axios.post('http://localhost:4003/events', event);

  axios.post('http://localhost:4006/events', event);
  axios.post('http://localhost:4007/events', event);

  res.send({ status: 'OK' });
});
```

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-03/04-second-virtual-machine.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-03/04-second-virtual-machine.jpg)

```js
// event-bus/index.js
app.post('/events', (req, res) => {
  const event = req.body;
  events.push(event);

  axios.post('http://localhost:4000/events', event);
  axios.post('http://localhost:4001/events', event);
  axios.post('http://localhost:4002/events', event);
  axios.post('http://localhost:4003/events', event);

  if(it is not 1 am) {
    axios.post('http://181.143.203.151/events', event);
    axios.post('http://181.143.203.152/events', event);
  }

  res.send({ status: 'OK' });
});
```

**[??? back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#table-of-contents)**

### Why Docker?

Issues

* Running our app right now makes big assumptions about our environment
* Running our app requires precise knowledge of how to start it (npm start)

Docker solves both these issues

* Containers wrap up everything that is needed for a program + how to start and run it
  [![](https://github.com/chesterheng/microservices-node-react/raw/master/section-03/05-docker-container.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-03/05-docker-container.jpg)

**[??? back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#table-of-contents)**

### Why Kubernetes?

Kubernetes is a tool for running a bunch of different containers
We give it some configuration to describe how we want our containers to run and interact with each other

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-03/06-kubernetes-cluster.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-03/06-kubernetes-cluster.jpg)

**[??? back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#table-of-contents)**

### Dockerizing the Posts Service

Here is the format of the Dockerfile:

| INSTRUCTION | arguments        | Comment                                              |
| ----------- | ---------------- | ---------------------------------------------------- |
| FROM        | node:alpine      | Specify base image                                   |
| WORKDIR     | /app             | Set the working directory to '/app' in the container |
| COPY        | package.json ./  | Copy over only the package.json file                 |
| RUN         | npm install      | Install all dependencies                             |
| COPY        | ./ ./            | Copy over all of our remaining source code           |
| CMD         | ["npm", "start"] | Set the command to run when the container starts up  |

**[??? back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#table-of-contents)**

### Review Some Basic Commands

| Docker Commands                              | Explanation                                                                                     |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| docker build -t chesterheng/posts .          | Build an image based on the dockerfile in the current directory.  Tag it as 'chesterheng/posts' |
| docker run [image id or image tag]           | Create and start a container based on the provided image id or tag                              |
| docker run -it [image id or image tag] [cmd] | Create and start container, but also override the default command                               |
| docker ps                                    | Print out information about all of the running containers                                       |
| docker exec -it [container id] [cmd]         | Execute the given command in a running container                                                |
| docker logs [container id]                   | Print out logs from the given container                                                         |

```
cd section-03/blog/posts
docker build -t chesterheng/posts .
docker run chesterheng/posts
docker run -it chesterheng/posts sh
docker ps
docker exec -it a643fdbf134e sh
docker logs a643fdbf134e
```

**[??? back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-03.md#table-of-contents)**

### Dockering Other Services

```
cd section-03/blog/event-bus
docker build -t chesterheng/event-bus .
docker run chesterheng/event-bus
docker run -it chesterheng/posts sh
docker ps
docker exec -it a643fdbf134e sh
docker logs a643fdbf134e
```
