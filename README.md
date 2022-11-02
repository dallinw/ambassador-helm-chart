# Ambassador Edge Stack

Configuration of Ambassador edge stack as an ingress controller, api-gateway and more.

## Requirements

- Kubernetes cluster
- Kubectl configured to connect to that cluster
- Helm

## Installation

### Add the Repo:
- helm repo add datawire https://www.getambassador.io
- helm repo update

### Install with Helm 3:

Create the namespace.

`kubectl create namespace ambassador`

Create all the custom Kubernetes CRD's.

`kubectl apply -f https://app.getambassador.io/yaml/edge-stack/latest/aes-crds.yaml`

Install the edge stack, note the custom token. This is what connects it to https://app.getambassador.io/cloud/clusters

`helm install edge-stack --namespace ambassador datawire/edge-stack --values values.yml --set emissary-ingress.agent.cloudConnectToken=<ENTER_YOUR_TOKEN_HERE>`

Watch the progress if you wish.
`kubectl -n ambassador wait --for condition=available --timeout=90s deploy -lproduct=aes`

## Configuration

### TLS Management

If you want TLS certificate issuance you must configure `Cert-Manager` with `Lets-Encrypt (ACME)`

https://cert-manager.io/docs/installation/helm/

Once you have it installed, you can reference the file `cluster-issuers.yaml` to find a valid and working configuration. Note however that this uses CloudDNS on the google cloud platform and uses the native integration and service accounts to validate domain ownership.

Follow the documentation here for a complete example.

https://cert-manager.io/docs/configuration/acme/dns01/google/

Important note, do not use the production letsencrypt unless you are 100% certain the config is valid for your cluster. Let's encrypt will blacklist a domain after a few failed attempts for at first a week than a month.

### Ingress Configuration

If you want to have host based routing (layer 7) you need to create and apply a host for Ambassador to listen on.

See the file `hosts.yaml` for an example.
