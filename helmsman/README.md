# helmsman

## Prerequsites
- helm
- helmsman

## Description
Desired state files and values for Helm chart installation

This process uses [Helmsman](https://github.com/Praqma/helmsman) which allows you to automate the deployment/management of your Helm charts from version controlled code.

```
.
├── README.md                       # this file
├── non-prod-management.yaml        # desired state file for non-prod-management cluster (monitoring/alerting)
├── test.yaml                       # desired state file for non-prod application cluster (application tenant)
└── values
    ├── grafana
    │   └── values-non-prod.yaml    # helm values for non-prod grafana chart installation
    ├── logstash
    │   └── values-non-prod.yaml    # helm values for non-prod logstash chart installation
    ├── prometheus-rules
    │   └── values-test.yaml        # helm values for non-prod prometheus-rules chart installation
    └── traefik
        └── values-test.yaml        # helm values for non-prod Traefik chart installation
```

## Usage

to test a desired state file 
```
# helmsman -f <desired_state> --check-for-chart-updates --no-ns --dry-run
# options;
# --check-for-chart-updates displays information on whether this chart can be updated
# --no-ns prevents helmsman from attempting to create a namespace if it doesn't exist
# --dry-run display the manifests rather than install them (omit this to install)

helmsman -f test.yaml --check-for-chart-updates --no-ns --dry-run
```
to install a desred state file
```
helmsman -f test.yaml --check-for-chart-updates --no-ns
```