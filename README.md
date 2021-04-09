Config repository to illustrate how the VotingApp can be deployed:
- on different environments
- using different tools


## Deployment on a simple machine with Docker Compose

Prerequisites: clone all repositories (config, vote, result, worker) in the same folder.

From *config/compose*, run the following command:

```
$ docker-compose up
```

This deploys the VotingApp using the worker's .NET version by default.

The *LANGUAGE* environment variable needs to be set to *java* to deploy the VotingApp with the Java version of the worker:

```
LANGUAGE=java docker-compose up
```

## Deployment on a Kubernetes cluster

In a Kubernetes context, the VotingApp is defined in several ways:
- yaml manifests file
- Helm chart
- Kustomize structure

### Using raw manifests files

Within the *config/manifests* folder:

```
$ kubectl apply -f .
```

### Using Helm

Within the *config/helm* folder:

```
$ helm upgrade voting --install --values values.yaml .
```

### Using Kustomize

Within the *config/kustomize* folder:

```
$ kubectl apply -k base/
```
