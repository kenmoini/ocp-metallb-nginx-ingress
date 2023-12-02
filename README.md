# OpenShift with MetalLB, NGINX Ingress, and Cert-Manager

This repository provides the ability to quickly deploy MetalLB, to use with the NGINX Ingress, and cert-manager - if so desired in a GitOps fashion.

## Prerequisites

This repository has a set of examples to work in my lab that will need to be adapted for your environment.  Namely you'll need to adapt the following after Forking the repo:

- `metallb/configuration/layer2/` - The assets in this directory need to be adjusted to your network
- `nginx-ingress/instance/instance-values.yml` - Some variables may need to be changed to your environment specifications, though the defaults should work fine.
- `cert-manager/instance/overlays/outbound-proxy/` - If using an Outbound Proxy you will need to modify to match your server specs.
- `test-deployment/cluster-issuer-letsencrypt.yml` - You'll need to modify the email to yours
- `test-deployment/service.yml` - Modify the intended IP of the Service object.
- `test-deployment/ingress.yml` - Modify the hostname of the intended Ingress object.

## Install MetalLB

```bash=
# Install the Operator
oc apply -k metallb/operator/base/

# Wait for the Operator to Install

# Deploy the MetalLB System Instance
oc apply -k metallb/instance/default/

# Or Deploy to Infrastructure Nodes
oc apply -k metallb/instance/infra-nodes/

# Deploy AddressPools and L2Advertisement objects
oc apply -k metallb/configuration/layer2/
```

## Install NGINX Ingress

```bash=
# Install the Operator
oc apply -k nginx-ingress/operator/base/

# Wait for the Operator to Install

# Deploy the NGINX Ingress Instance
oc apply -k nginx-ingress/instance/
```

## Install Cert-Manager

```bash=
# Install the Operator
oc apply -k cert-manager/operator/overlays/stable-v1/

# Wait for the Operator to Install

# Deploy the Cert-Manager Instance
oc apply -k cert-manager/instance/overlays/default/

# Or, deploy with an Outbound Proxy
oc apply -k cert-manager/instance/overlays/outbound-proxy/
```

## Test Workload Deployment

Also included in this repository is a set of manifests to test the Operators.  Included is a Deployment/Service/Ingress in the `workload-test` Namespace/Project as well as a Cert-Manager ClusterIssuer that uses Let's Encrypt.

```bash=
# Deploy the Test Workload HTTP Server
oc apply -k test-deployment/
```

You can access the application via the Service LoadBalancer and test by either `curl`'ing or loading `http://IP_ADDRESS_HERE:8080/` in your browser.  You should see the Apache HTTPd Welcome Page.

So long as your DNS is pointing to the LoadBalancer IP, you can also test the Ingress object via HTTP{S}.