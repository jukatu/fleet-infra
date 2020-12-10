# fleet-infra

## Staging Environment
```bash
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>

# Create an Ingress ready cluster
cat <<EOF | kind create cluster --name staging --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 8080
    protocol: TCP
  - containerPort: 443
    hostPort: 8443
    protocol: TCP
EOF

kubectl cluster-info --context kind-staging

flux check --pre --context kind-staging

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=fleet-infra \
  --branch=main \
  --path=staging-cluster \
  --personal \
  --context kind-staging


watch flux get kustomizations --context kind-staging

kubectl --context kind-staging -n webapp get deployments,services

```

## Production Environment
```bash
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>

# Create an Ingress ready cluster
cat <<EOF | kind create cluster --name production --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF

kubectl cluster-info --context kind-production

flux check --pre --context kind-production
```

## References
* [Get started with Flux v2](https://toolkit.fluxcd.io/get-started/)
* [Setup Ingress with kind](https://kind.sigs.k8s.io/docs/user/ingress/#using-ingress)