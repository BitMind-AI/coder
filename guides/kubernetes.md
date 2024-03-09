# Coder on Kubernetes Installation Guide

## Step 1: Install kubectl

kubectl is the command-line tool for interacting with Kubernetes clusters.

1. Update your package list and install prerequisites:
```sh
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2 curl
```
2. Add the Kubernetes signing key:
```sh
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```
3. Add the Kubernetes repository:
```sh
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```
4. Update your package list and install kubectl:
```sh
sudo apt-get update
sudo apt-get install -y kubectl
```

## Step 2: Install Minikube

Minikube allows you to run Kubernetes locally.

1. Download Minikube:
```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```
2. Install Minikube:
```sh
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

## Step 3: Start Minikube

Start your local Kubernetes cluster with Minikube:
```sh
minikube start
```
This command may take some time as it downloads necessary components.

## Step 4: Install Docker (Optional)

Docker is required for building and managing containers.
- Follow the [official Docker installation instructions for Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

## Step 5: Install Helm (Optional)

Helm is a package manager for Kubernetes applications.
```sh
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

## Step 6: Configure kubectl

Ensure kubectl is configured to use Minikube:
```sh
kubectl config current-context
kubectl config use-context minikube
```

## Verifying the Installation

Verify your Kubernetes cluster by listing the nodes:
```sh
kubectl get nodes
```

## Install Coder with Helm

To deploy Coder, a cloud development platform, follow these steps:

1. Create a namespace for Coder:
```sh
kubectl create namespace coder
```

2. Install PostgreSQL using the Bitnami Helm chart:
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install coder-db bitnami/postgresql --namespace coder --set auth.username=coder --set auth.password=coder --set auth.database=coder --set persistence.size=10Gi
```

3. Create a secret with the database URL:
```sh
kubectl create secret generic coder-db-url -n coder --from-literal=url="postgres://coder:coder@coder-db-postgresql.coder.svc.cluster.local:5432/coder?sslmode=disable"
```

4. Add the Coder Helm repo and install Coder:
```sh
helm repo add coder-v2 https://helm.coder.com/v2
helm install coder coder-v2/coder --namespace coder --values values.yaml
```
Adjust `values.yaml` according to your preferences, detailing configurations like environment variables and access URLs.

For detailed instructions and additional configurations, please visit [Coder's official Kubernetes installation guide](https://coder.com/docs/v2/latest/install/kubernetes).

## Upgrading Coder via Helm

To upgrade Coder:
```sh
helm repo update
helm upgrade coder coder-v2/coder --namespace coder -f values.yaml
```

## Kubernetes Security Considerations

Ensure your Kubernetes deployment adheres to security best practices such as:
- Using internal container registries
- Running containers as non-root
- Disabling privileged containers
- Setting resource requests/limits
- Defining liveness and readiness probes

## Troubleshooting

If you encounter issues, check Coder's logs:
```sh
kubectl logs <pod name>
```
Ensure that `CODER_ACCESS_URL` is set correctly for workspace connectivity.
