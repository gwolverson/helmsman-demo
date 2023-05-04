# Helmsman Demo

This project was built to accomany a blog post I wrote, which gives a high level view as to how Helmsman works. 

Within this repository are the following three projects;

- helmsman
- sample-spring-boot-app
- service-helm-chart

## Helmsman

This project contains a Helmsman project, defining a desired state file for 4 environments; dev, test, preprod and prod. It also contains the relevant values file for the apps being deploy; 2 spring boot microservices and a MySQL service (no schema or functionality defined within). The project structure is as follows:

```
.
├── dev.yaml        # desired state file for development environment (non-production)
├── test.yaml       # desired state file for test environment (non-production)
|── preprod.yaml    # desired state file for pre-production environment (production)
|── prod.yaml       # desired state file for production environment (production)
└── values
    ├── mysql
        └── values-dev.yaml        # helm values for the mysql development chart installation
        └── values-test.yaml       # helm values for the mysql test chart installation
        └── values-preprod.yaml    # helm values for the mysql preprod chart installation
        └── values-prod.yaml       # helm values for the mysql prod chart installation
    ├── service-a
        └── values-dev.yaml        # helm values for the service-a development chart installation
        └── values-test.yaml       # helm values for the service-a test chart installation
        └── values-preprod.yaml    # helm values for the service-a preprod chart installation
        └── values-prod.yaml       # helm values for the service-a prod chart installation
    ├── service-b
        └── values-dev.yaml        # helm values for the service-b development chart installation
        └── values-test.yaml       # helm values for the service-b test chart installation
        └── values-preprod.yaml    # helm values for the service-b preprod chart installation
        └── values-prod.yaml       # helm values for the service-b prod chart installation
```

## Sample Spring Boot App

This project just contains a very simple spring boot application (no functionality).

## Service Helm Chart

This project contains a base helm application chart, that is used as the base chart for deploying the services listed in the Helmsman state files. It contains the following Kubernetes manifest templates:

- Deployment
- Network Policy
- Service

## Spinning It Up

>Note: You will need a running kubernetes cluster, I have used [Rancher Desktop](https://rancherdesktop.io/) to test the project.

Although the demo has no working functionality, you can spin it up to get a feel for how Helmsman works by following these steps:

### Build The Spring Boot Image

From the root of the `sample-spring-boot-app` project, run the following command:

`docker build -t service:1.0.0 .`

### Helmsman Commands

The following need to be done from within the root of the `helmsman` project. 

To do `dry-run` of a Helmsman deploy, you can run the following command:

`helmsman -f dev.yaml --dry-run`

This will show you what *will* be installed into your local kubernetes cluster when running the `apply` command.

To apply the desired state file, run:

`helmsman -f dev.yaml --apply`

You don't need to have created any namespace(s) beforehand as Helmsan can do this for you. 

### Checks

You can do some basic checks to see that the Spring Boot services have different configurations (as per our Helmsman setup)

#### Check Resources

Service A & B *should* have different resources set on them:

Checking `service-a` deployed resources:

`k get deploy service-a -n dev -o jsonpath="{.spec.template.spec.containers[0].resources}"` 

Should show:

```
{
    "limits": {
        "cpu":"300m",
        "memory":"350Mi"
    },
    "requests":{
        "cpu":"250m",
        "memory":"250Mi"
    }
}
```

The same can be checked for `service-b`:

`k get deploy service-b -n dev -o jsonpath="{.spec.template.spec.containers[0].resources}"` 

#### Check Network Policies

There should also be differences between the 2 services' network policies, you can check the network policy content like so:

Service-A

`k get netpol service-a-network-policy -n dev -o yaml` 

Service-B

`k get netpol service-b-network-policy -n dev -o yaml` 

You should notice the difference when you check.
