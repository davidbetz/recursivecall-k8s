# Kubernetes Configuration for RecursiveCall

## Summary

See [https://github.com/davidbetz/recursivecall-go](https://github.com/davidbetz/recursivecall-go) for project details.

This setup has a few components:

* Services
* Deployments
* NGINX Ingress Controller
* Ingress

## Usage

    kubectl apply -f services.yml
    kubectl apply -f deployments-go.yml
    kubectl apply -f ingress-controller-go.yml
    kubectl apply -f ingress-go.yml

After adding `recursivecall.example.org` to your name resolution (e.g. `/etc/hosts`):

    curl recursivecall.example.org:30000

Also:

    curl recursivecall.example.org:30000/g
    curl recursivecall.example.org:30000/c
    curl recursivecall.example.org:30000/a

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
