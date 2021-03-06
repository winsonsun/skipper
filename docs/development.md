# Development Guidelines

## Local Setup

This is a node.js project that requires v8.3.0 or higher

```
nvm use v8.3.0
```

Install the following global packages

```
npm install -g mocha istanbul@1.1.0-alpha.1
```

## Run tests

Tests assume that you have minikube started and running ingress and that the proxy to the API server is running.

```
minikube start
minikube addons enable ingress
kubectl proxy
```

```
npm test
```

If you get "Error initializing middleware" then check that you have node version `>= 8.3` selected w/ nvm.

Tests use mocha and sometimes it can be helpful to rerun a specific set of tests. 

For example, to run tests that leave a service running you can run:

```text
mocha -g example 
#example test suite leaves the service running for manual confimation
```

# hit the running service endpoint

`curl -k https://mydeployment-default.192.168.99.100.xip.io/`


## Hoverfly

By default tests will run against minikube on 192.168.99.100. If you prefer to run against a mock of k8s you can use [hoverfly](https://hoverfly.io/).

```
hoverctl start --proxy-port 8500
hoverctl import ./hoverfly/k8s.json
hoverctl mode simulate --matching-strategy first
export mockproxy=http://127.0.0.1:8500 #used by mocha tests
```

## Testing against a custom set of clusters

You can also specify custom variables for tests to run against your own kubernetes infrastructure using the following environment variables.

- kubelocation
- kubecluster
- kubenamespace
- kubetoken

The filestore or dynamodb databases must already contain endpoint information for the clusters in the provided custom location prior to the test.

See the admin guide for details on how to configure those.

## Run tests inside the Docker container

```
docker run -it -e storagetype -e ddbtable -e customValidateUpdateIndex -e awskey -e awssecret -e domain -e defaultLocations -e defaultHealthCheck -e dynamicServiceAnnotations -e srcReplacementRepos -e setupLocationFilters -e customLocationProcessingHandler -e kubelocation -e kubecluster -e kubenamespace -e kubetoken concur/skipper:latest sh -c "npm install -g mocha && mocha"
```

# submit pull request

Please squash commits and ensure local tests pass before you submit a pull request.
