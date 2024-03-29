This project is a clone of the official [Docker Example Voting App](https://github.com/dockersamples/example-voting-app) used for tests and demo purposes only.

This repository is used to illustrate how the different versions of the VotingApp can be deployed:
- on different environments
- using different tools

First of all let's talk a little bit about this simple thus very interesting demo application.

# What is the VotingApp

The VotingApp is a *demo* application following a micro-services architecture. 

It does not follow all good architectural best practices but it is a good example of an application using several languages, several databases and definitely a great one to learn Docker and Kubernetes related concepts (and much more)

There are currently 3 different versions of this application:
- v1: original version
- v2: new frontends for result and vote
- v3: same as v2 but using NATS instead of Redis+Postgres+Worker

## Original version (legacy)

This is the first version of the VotingApp. It is composed of 5 micro-services as illustrated in the following schema.

![Architecture](./picts/architecture-v1.png)

- vote: front end in Python / Flask that enables a user to choose between a cat and a dog
- redis: database where votes are stored
- worker: service that get votes from Redis and store the results in a Postgres database (a Java ,.NET and Go version exist for that service)
- db: the Postgres database in which vote results are stored
- result: front end, displaying the results of the vote

Note: this version might be removed in the future

## v2: new frontends

In this version, 2 additional services were added: *vote-ui* and *result-ui*, the communicate respectively with enhanced versions of *vote* and *result*

The VotingApp v2 is thus composed of 7 microservices as illustrated in the following schema:

![Architecture](./picts/architecture-v2.png)

- vote-ui: front end in VueJS that enables a user to choose between a cat and a dog
- vote: backend exposing an API with Python / Flask 
- redis: database where votes are stored
- worker: service that get votes from Redis and store the results in a Postgres database (a Java ,.NET and Go version exist for that service)
- db: the Postgres database in which vote results are stored
- result: backend sending scores to a UI through websocket
- result-ui: front end in Angular displaying the results of the votes

## v3: NATS backend

This version does not use Redis, Postgres and the worker services anymore. NATS is used as a Pub/Sub mecanism instead for the *vote* and *result* microservice to communicate.

The VotingApp v3 is thus composed of 5 microservices as illustrated in the following schema.

![Architecture](./picts/architecture-v3.png)

- vote-ui: front end in VueJS that enables a user to choose between a cat and a dog
- vote: backend exposing an API with Python / Flask 
- nats: message broker acting as a Pub/Sub between vote and result
- result: backend sending scores to a UI through websocket
- result-ui: front end in Angular displaying the results of the votes


# Get it locally

Use the following commands to clone all the projects in a local folder

```
mkdir VotingApp && cd VotingApp
for project in config vote vote-ui result result-ui worker; do
  git clone https://gitlab.com/voting-application/$project
done
```

# The projects repositories

In the official GitHub repository, all the projects are under the same umbrella. To clarify the things and make it easier to map it against a real micro-service application, we've splitted the application so that each micro-service has it's own Git repository.

- vote (v1, v2 and v3 branches)
- vote-ui
- result (v1, v2 and v3 branches)
- result-ui
- worker

# The configuration repository

The *config* repository is a new repository that has been added, it is used to deploy the whole application in several ways in a Kubernetes or Docker (Compose / Swarm) environments:

## Running the app with Docker Compose

Prerequisites: clone all repositories (config, vote, result, worker) in the same folder.

From *config/compose*, run the following command:

```
docker compose up
```

This deploys the application using the branches currently checked out for each microservice.

## Running the app with Kubernetes manifests

The *manifests* folder contains the yaml specifications of the latest release of the VotingApp. Deploy it using the following command:

```
kubectl apply -f manifests
```

## Running the app with [Helm](https://helm.sh)

The application can be installed using [Helm](https://helm.sh) with the following command within the *helm* folder:

```
helm upgrade --install -f values.yaml voting .
```

*values.yaml* contains properties that enable to configure the version that should be deployed

Also, for demo purposes, the Helm chart of the VotingApp are distributed in 2 different locations:

- GitLab Package registry
- DockerHub (oci registry)

The application can be installed from the GitLab package using the following command:

```
helm repo add votingapp https://gitlab.com/api/v4/projects/25309838/packages/helm/stable
helm install votingapp votingapp/votingapp
```

Or it can be installed from the DockerHub:

```
helm install votingapp oci://registry-1.docker.io/lucj/votingapp --version v1.0.98
```

## Running the app with [Acorn](https://acorn.io)

Acorn is a brand new platform which allow to build, run and share application. With a GitHub account you can deploy the VotingApp by a click on a button :)

[![Run in Acorn](https://acorn.io/v1-ui/run/badge?image=docker.io+lucj+voting:v%23.%23.%23)](https://acorn.io/run/docker.io/lucj/voting:v%23.%23.%23)
