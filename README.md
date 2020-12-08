# fleet-infra

```bash
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>

kind create cluster --name staging
kubectl cluster-info --context kind-staging

flux check --pre

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=fleet-infra \
  --branch=main \
  --path=staging-cluster \
  --personal


watch flux get kustomizations

kubectl -n webapp get deployments,services

```