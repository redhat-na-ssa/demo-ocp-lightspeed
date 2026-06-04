# Demo Lightspeed on OpenShift

## Prerequisites - Get a cluster

- OpenShift 4.16+
  - role: `cluster-admin` - for all [demo](demos) or [cluster](clusters) configs
  - role: `self-provisioner` - for namespaced components

[Red Hat Demo Platform](https://demo.redhat.com) Options (Tested)

NOTE: The node sizes below are the **recommended minimum** to select for provisioning

- <a href="https://demo.redhat.com/catalog?item=babylon-catalog-prod/sandboxes-gpte.sandbox-ocp.prod&utm_source=webapp&utm_medium=share-link" target="_blank">AWS with OpenShift Open Environment</a>
  - 1 x Control Plane - `m6a.2xlarge`
  - 0 x Workers - `m6a.2xlarge`
  - 1 x GPU - `g6.2xlarge` or `g6e.2xlarge`
- <a href="https://demo.redhat.com/catalog?item=babylon-catalog-prod/sandboxes-gpte.ocp4-single-node.prod&utm_source=webapp&utm_medium=share-link" target="_blank">One Node OpenShift</a>
  - 1 x Control Plane - `m6a.2xlarge`
- <a href="https://demo.redhat.com/catalog?item=babylon-catalog-prod/community-content.com-mlops-wksp.prod&utm_source=webapp&utm_medium=share-link" target="_blank">MLOps Demo: Data Science & Edge Practice</a>

## Getting Started

### Install the [OpenShift Web Terminal](https://docs.openshift.com/container-platform/4.12/web_console/web_terminal/installing-web-terminal.html)

The following icon should appear in the top right of the OpenShift web console after you have installed the operator. Clicking this icon launches the web terminal.

![Web Terminal](docs/images/web-terminal.png "Web Terminal")

NOTE: Reload the page in your browser if you do not see the icon after installing the operator.

Make the enhanced web terminal permanent

```sh
# apply the enhanced web terminal
oc apply -k https://github.com/redhat-na-ssa/demo-ocp-lightspeed/gitops/operators/web-terminal

# delete old web terminal
$(wtoctl | grep 'oc delete')
```

Setup cluster nodes

```sh
# isolate the control plane
ocp_control_nodes_not_schedulable

# setup L40 single GPU machine set
ocp_aws_machineset_create_gpu g6.2xlarge

# scale machineset to at least 1
ocp_machineset_scale 1
```

Deploy the self hosted demo

```sh
# setup self hosted demo
apply_firmly gitops
```
