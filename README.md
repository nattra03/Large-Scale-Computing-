## Authors
Natalia Trąba, Patrycja Markiewicz

## Project topic
Argo Workflows: DAG Pipelines on Kubernetes Build and benchmark a multi-stage data or ML 
pipeline using Argo Workflows on Kind, demonstrating DAG vs. step execution models, artifact passing strategies, and retry/fault-tolerance behavior.

## Local Kubernetes cluster setup and installation of Argo Workflows
The goal of this stage was to prepare a local cloud-like environment that simulates a production Kubernetes infrastructure. 
Within this environment, a workflow orchestration system - Argo Workflows, was installed and configured using a locally deployed Kubernetes cluster created with Kind.

This setup enables the execution and testing of data and machine learning pipelines in a DAG model, including artifact passing, retry mechanisms, and fault-tolerance behavior.

1. Verification of required tools

```bash
kubectl version --client
```
kubectl - Kubernetes command-line client used to manage clusters

```bash
kind --version
```
kind - a tool for running local Kubernetes clusters using Docker containers

2. Creating a local Kubernetes cluster

```bash
kind create cluster --name argo-cluster
```

As a result a Kubernetes cluster named argo-cluster was created. A control-plane node was started inside a Docker container and 
kubectl was automatically configured to interact with the new claster.

```bash
kubectl get nodes
```
This command confirmed that the cluster was running and ready for use. We waited until its status changed from NotReady to Ready,
which indicated that the cluster networking systems had started correctly and all core Kubernetes components were successfully initialized.

3. Installation of Argo Workflows and MinIO

```bash
kubectl create namespace argo
```
A dedicated namespace was created to isolate Argo components from other Kubernetes resources.

```bash
kubectl apply --server-side -n argo -f https://github.com/argoproj/argo-workflows/releases/latest/download/quick-start-postgres.yaml
```
This command deployed Argo Workflows along with its required components - Argo server, Web UI, PostgreSQL database, Object storage system MinIO.

```bash
kubectl get pods -n argo
```
Used to monitor the status of deployed components (pods). The system was considered ready once all pods reached the Running state.
