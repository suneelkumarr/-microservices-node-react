## **Section 23: CI/CD**

## Table of Contents

* [**Section 23: CI/CD**](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#section-23-cicd)
* [Table of Contents](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)
  * [Development Workflow](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#development-workflow)
  * [Git Repository Approaches](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#git-repository-approaches)
  * [Creating a GitHub Action](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#creating-a-github-action)
  * [Adding a CI Test Script](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#adding-a-ci-test-script)
  * [Running Tests on PR Creation](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#running-tests-on-pr-creation)
  * [Output of Failing Tests](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#output-of-failing-tests)
  * [Running Tests in Parallel](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#running-tests-in-parallel)
  * [Verifying a Test Run](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#verifying-a-test-run)
  * [Selective Test Execution](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#selective-test-execution)
  * [Deployment Options](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#deployment-options)
  * [Creating a Hosted Cluster](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#creating-a-hosted-cluster)
  * [Reminder on Kubernetes Context](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#reminder-on-kubernetes-context)
  * [Reminder on Swapping Contexts](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#reminder-on-swapping-contexts)
  * [The Deployment Plan](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#the-deployment-plan)
  * [Building an Image in an Action](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#building-an-image-in-an-action)
  * [Testing the Image Build](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#testing-the-image-build)
  * [Restarting the Deployment](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#restarting-the-deployment)
  * [Applying Kubernetes Manifests](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#applying-kubernetes-manifests)
  * [Prod vs Dev Manifest Files](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#prod-vs-dev-manifest-files)
  * [Manual Secret Creation](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#manual-secret-creation)
  * [Don&#39;t Forget Ingress-Nginx!](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#dont-forget-ingress-nginx)
  * [Testing Automated Deployment](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#testing-automated-deployment)
  * [Additional Deploy Files](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#additional-deploy-files)
  * [A Successful Deploy!](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#a-successful-deploy)
  * [Buying a Domain Name](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#buying-a-domain-name)
  * [Configuring the Domain Name](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#configuring-the-domain-name)
  * [One Small Fix](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#one-small-fix)
  * [One More Small Fix](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#one-more-small-fix)
  * [I Really Hope This Works](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#i-really-hope-this-works)
  * [Next Steps](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#next-steps)

### Development Workflow

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/teams.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/teams.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/branch.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/branch.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Git Repository Approaches

* Mono Repo Approach (selected)
* Repo-Per-Service Approach (not selected, many overheads)

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/mono-repo.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/mono-repo.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/repo-per-service.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/repo-per-service.jpg)

* copy ticketing folder to Desktop
* open ticketing folder with VS Code

```
git init
```

* create .gitignore

```
node_modules
.DS_Store
```

```
git add .
git commit -m "initial commit"
```

* goto github
* create a new repo: ticketing
* goto ticketing folder

```
git remote add origin https://github.com/chesterheng/ticketing.git
git push origin master
```

* Refer to [https://github.com/chesterheng/ticketing](https://github.com/chesterheng/ticketing)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Creating a GitHub Action

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/github-action.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/github-action.jpg)

* [GitHub Actions Documentation](https://docs.github.com/en/actions)
* [Events that trigger workflows](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

```yaml
name: tests

on: 
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd auth && npm install && npm run test:ci
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Adding a CI Test Script

* to run test only one time

```json
  "test:ci": "jest"
```

```
git status
git add .
git commit -m "463. Adding a CI Test Script"
git pull origin master
git push origin master
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Running Tests on PR Creation

Local Machine

* Make change to code for tickets service
* Commit code to a git branch (any besides master!)

```
git branch -m 464-running-tests-on-pr-creation
git add .
git commit -m "464. Running Tests on PR Creation"
git push --set-upstream origin 464-running-tests-on-pr-creation
```

* Push branch to github
  [![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/run-action-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/run-action-1.jpg)
  [![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/run-action-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/run-action-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Output of Failing Tests

* Change expect result to make test fails

```
git add .
git commit -m "465. Output of Failing Tests"
git push
```

Github

* Github receives updated branch
* You manually create a pull request to merge branch into master
* Github automatically runs tests for project

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Running Tests in Parallel

```yaml
name: tests-orders

on: 
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd orders && npm install && npm run test:ci
```

```yaml
name: tests-payments

on: 
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd payments && npm install && npm run test:ci
```

```yaml
name: tests-tickets

on: 
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd tickets && npm install && npm run test:ci
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Verifying a Test Run

* make a change and commit
* push a PR and see 4 tests run in parallel

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Selective Test Execution

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/selective-test-execution.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/selective-test-execution.jpg)

* paths parameter
* run auth test if path change is auth folder

```yaml
name: tests-auth

on: 
  pull_request:
    paths:
      - 'auth/**'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd auth && npm install && npm run test:ci
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Deployment Options

* A credit card will be required to move forward
* $1 for a domain name, $0.72 a day to run the cluster
* You can probably find a coupon code to pay $0 for the cluster

3 nodes, each with 2gb ram + 1 cpu PLUS EXTRAS

| Digital Ocean                   | AWS                      | Google Cloud | Azure |
| ------------------------------- | ------------------------ | ------------ | ----- |
| $40/month          | $126/month | $113/month   | $72/month |              |       |
| Really easy to use              | Hardest                  | Easy         | Easy  |

* Digital Ocean is selected

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Creating a Hosted Cluster

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/choose-cluster-capacity.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/choose-cluster-capacity.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Reminder on Kubernetes Context

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/context.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/context.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/doctl.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/doctl.jpg)

* [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [doctl](https://github.com/digitalocean/doctl#installing-doctl)
* doctl auth init

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/token.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/token.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Reminder on Swapping Contexts

| Action                                  | Command                                                 |
| --------------------------------------- | ------------------------------------------------------- |
| Authenticating with Doctl               | doctl auth init                                         |
| Get connection info for our new cluster | doctl kubernetes cluster kubeconfig save <cluster_name> |
| List all contexts                       | kubectl config view                                     |
| Use a different context                 | kubectl config use-context <context_name>               |

```
doctl kubernetes cluster kubeconfig save ticketing
kubectl get pods
kubectl get nodes
kubectl config view
kubectl config use-context docker-desktop
kubectl get nodes
kubectl get pods
```

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/context-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/context-2.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### The Deployment Plan

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/deployment-plan.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/deployment-plan.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Building an Image in an Action

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/secrets.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/secrets.jpg)

```yaml
name: deploy-auth

on:
  push:
    branches: 
      - master
    paths:
      - 'auth/**'

jobs:
  build:
   runs-on: ubuntu-latest
   steps:
     - uses: actions/checkout@v2
     - run: cd auth && docker build -t chesterheng/auth .
     - run: docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
       env:
         DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
         DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
     - run: docker push chesterheng/auth
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Testing the Image Build

* After merge to master
* Build a new auth image
* Push to docker hub

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Restarting the Deployment

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/restart-deployment.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/restart-deployment.jpg)

```yaml
name: deploy-auth

on:
  push:
    branches: 
      - master
    paths:
      - 'auth/**'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd auth && docker build -t chesterheng/auth .
      - run: docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
        env:
          DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
          DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      - run: docker push chesterheng/auth
      - uses: digitalocean/action-doctl@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
      - run: doctl kubernetes cluster kubeconfig save ticketing
      - run: kubectl rollout restart deployment auth-depl
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Applying Kubernetes Manifests

```yaml
name: deploy-manifests

on:
  push:
    branches: 
      - master
    paths:
      - 'infra/**'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: digitalocean/action-doctl@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
      - run: doctl kubernetes cluster kubeconfig save ticketing
      - run: kubectl apply -f infra/k8s
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Prod vs Dev Manifest Files

* create k8s-dev folder to store ingress-srv.yaml for development environmemt
* create k8s-prod folder to store ingress-srv.yaml for production environmemt
* 

```yaml
name: deploy-manifests

on:
  push:
    branches: 
      - master
    paths:
      - 'infra/**'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: digitalocean/action-doctl@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
      - run: doctl kubernetes cluster kubeconfig save ticketing
      - run: kubectl apply -f infra/k8s && kubectl apply -f infra/k8s-prod
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Manual Secret Creation

```
kubectl config view
kubectl config use-context do-sgp1-ticketing
kubectl create secret generic jwt-secret --from-literal=JWT_KEY=sadghgjgshdajh
kubectl create secret generic stripe-secret --from-literal=STRIPE_KEY=sk_test_...
kubectl get sesrets
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Don't Forget Ingress-Nginx!

[NGINX Ingress Controller - Digital Ocean](https://kubernetes.github.io/ingress-nginx/deploy/#digital-ocean)

```
kubectl config view
kubectl config use-context do-sgp1-ticketing
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/do/deploy.yaml
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Testing Automated Deployment

* make a change in auth folder
* commit the change and do a PR
* merge to master and deploy-auth action will execute

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Additional Deploy Files

```
kubectl get pods
kubectl logs auth-depl-db79bbf7f-dsjnr
```

Add in the following similar deploy files

* deploy-client.yaml
* deploy-expiration.yaml
* deploy-orders.yaml
* deploy-payments.yaml
* deploy-tickets.yaml

```yaml
name: deploy-client

on:
  push:
    branches:
      - master
    paths:
      - 'client/**'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: cd client && docker build -t chesterheng/client .
      - run: docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
        env:
          DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
          DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      - run: docker push chesterheng/client
      - uses: digitalocean/action-doctl@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
      - run: doctl kubernetes cluster kubeconfig save ticketing
      - run: kubectl rollout restart deployment client-depl
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### A Successful Deploy!

* make changes in auth folder
* make changes in client folder
* make changes in expiration folder
* make changes in orders folder
* make changes in payments folder
* make a PR and merge

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/successful-deploy.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/successful-deploy.jpg)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Buying a Domain Name

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/load-balancer-1.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/load-balancer-1.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/load-balancer-2.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/load-balancer-2.jpg)

* [namecheap](https://www.namecheap.com/)

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Configuring the Domain Name

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/domain-name.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/domain-name.jpg)

* add digitalocean DNS to domain name
  * ns1.digitalocean.com
  * ns2.digitalocean.com
  * ns3.digitalocean.com

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/add-domain-name.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/add-domain-name.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/configure-domain.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/configure-domain.jpg)

* update live domain name at ingress-srv.yaml

```yaml
# k8s/ingress-srv.yaml
- host: www.chesterheng.xyz
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### One Small Fix

[Accessing pods over a managed load-balancer from inside the cluster](https://github.com/digitalocean/digitalocean-cloud-controller-manager/blob/master/docs/controllers/services/examples/README.md#accessing-pods-over-a-managed-load-balancer-from-inside-the-cluster)

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-service
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: 'true'
spec:
  rules:
    - host: www.chesterheng.xyz
      http:
        paths:
          - path: /api/payments/?(.*)
            backend:
              serviceName: payments-srv
              servicePort: 3000
          - path: /api/users/?(.*)
            backend:
              serviceName: auth-srv
              servicePort: 3000
          - path: /api/tickets/?(.*)
            backend:
              serviceName: tickets-srv
              servicePort: 3000
          - path: /api/orders/?(.*)
            backend:
              serviceName: orders-srv
              servicePort: 3000
          - path: /?(.*)
            backend:
              serviceName: client-srv
              servicePort: 3000
---
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/do-loadbalancer-enable-proxy-protocol: 'true'
    service.beta.kubernetes.io/do-loadbalancer-hostname: 'www.chesterheng.xyz'
  labels:
    helm.sh/chart: ingress-nginx-2.0.3
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/version: 0.32.0
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: controller
  name: ingress-nginx-controller
  namespace: ingress-nginx
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/component: controller
```

```js
// build-client.js
import axios from 'axios';

export default ({ req }) => {
  if(typeof window === 'undefined') {
    // we are on the server

    return axios.create({
      baseURL: 'http://www.chesterheng.xyz',
      headers: req.headers
    });
  } else {
    // we are on the browser

    return axios.create({
      baseURL: ''
    });
  }
};
```

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### One More Small Fix

You may recall that we configured all of our services to
only use cookies when the user is on an HTTPS connection.  This will
cause auth to fail while we do this initial deploy of our app, since we
don't have HTTPS setup right now.

To disable the HTTPS checking, go to the app.ts file in
the auth, orders, tickets, and payments services.  At the cookie-session
 middleware, change the following:

> secure: process.env.NODE_ENV !== 'test',

to:

> secure: false,

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### I Really Hope This Works

Goto chrome. Key in [www.chesterheng.xyz](http://www.chesterheng.xyz) to test

**[⬆ back to top](https://github.com/chesterheng/microservices-node-react/blob/master/section-23.md#table-of-contents)**

### Next Steps

Destroy

* load balancer - $10/month
* kubernetes cluster - $30/month

[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/destroy-load-balancer.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/destroy-load-balancer.jpg)
[![](https://github.com/chesterheng/microservices-node-react/raw/master/section-23/destroy-kubernetes-cluster.jpg)](https://github.com/chesterheng/microservices-node-react/blob/master/section-23/destroy-kubernetes-cluster.jpg)

| Feature                                                                  | Details                                                                                                        |
| ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------- |
| Add in HTTPS                                                             | See cert-manager.io                                                                                            |
| Add in Email Support                                                     | Send a user an email after they have paid for an order.  Create a new service using Mailchimp/Sendgrid/similar |
| Add in 'build' steps for our prod cluster                                | Right now we are still running our services + the client in 'dev'                                              |
| mode.  Add in additional Dockerfiles to build each service prior to      |                                                                                                                |
| deployment                                                               |                                                                                                                |
| Create a staging cluster                                                 | Our teammates might want to test out our app manually before we                                                |
| deploy it.  Maybe we could add in a new Github workflow to watch for     |                                                                                                                |
| pushes to a new branch of 'staging'.  Create a new cluster that you will |                                                                                                                |
| deploy to when you push to this branch                                   |                                                                                                                |
