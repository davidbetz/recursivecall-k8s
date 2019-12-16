# Kubernetes Configuration for RecursiveCall

## Summary

See [https://github.com/davidbetz/recursivecall-go](https://github.com/davidbetz/recursivecall-go) for project details.

This setup has a few components:

* Services
* Deployments
* NGINX Ingress Controller
* Ingress
* Network Policy
* ConfigMap (for environment variables)
* CronJob

Ingress controller service type is set to LoadBalancer for cloud. Change for non-cloud.

## Usage

    kubectl apply -f ingress-controller.yml
    kubectl apply -f services.yml
    kubectl apply -f .

Also:

    curl ADDRESS
    curl ADDRESS/g
    curl ADDRESS/c
    curl ADDRESS/a

## Probes

`deployments-go.yml` represents a deployment with readiness and liveness probes, so the entire infrastructure will take some time to come online. This is by design.

## Switching deployments

Switching from `deployments-go.yml` to `deployments-js.yml` is analagous to doing an upgrade.

The procedure is:

    Deploy deployments-js.yml
    Verify it's running
    Switch services.yml labels to point to deployments-js deployments.
    Delete deployments-go deployments.

Commands:

    kubectl apply -f deployments-js.yml
    kubectl get deployment -l app.kubernetes.io/version=js
    sed "s/version: go/version: js/g" services.yml | kubectl apply -f -
    kubectl delete -f deployments-go.yml
