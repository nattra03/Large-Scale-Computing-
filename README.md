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

## DAG-based ML pipeline implementation

After setting up the environment, a custom machine learning pipeline was implemented using Argo Workflows DAG model. The pipeline is designed to process any tabular dataset provided via URL.

1. Pipeline structure

The DAG pipeline consists of three main components:

- Automated preprocessing (missing value imputation, one-hot encoding, feature scaling)
- Parallel training of models (Logistic Regression, Decision Tree)
- Evaluation step (comparison of model accuracy, selection of best model)

2. Fault tolerance mechanism

To simulate real-world distributed system behavior, a random failure injection mechanism (around 33% probability) was implemented in ML tasks.
This allowed testing of retry mechanisms, fault tolerance behavior, workflow resilience. Argo automatically retries failed tasks up to a defined limit.

3. Artifact handling

Model outputs (accuracy values) were stored as artifacts and persisted using MinIO object storage, which acts as an S3-compatible backend.
This enables persistence of experiment results, reproducibility, external storage of workflow outputs.

## Step-based pipeline implementation

In addition to the DAG model, a second pipeline was implemented using the Steps execution model.

1. Pipeline structure

Unlike DAG, the step-based pipeline executes tasks - identical to DAG's, in a sequential manner. Each step runs after the previous one completes.

2. Fault tolerance mechanism

The same random failure mechanism was applied. Argo handled failures using step-level retry logic, ensuring failed steps are re-executed, completed steps are preserved, workflow continues from failure point.


## Workflow execution

1. Submitting the workflow

```bash
kubectl create -f ml-pipeline.yaml
```
This command submits the workflow to the Argo cluster and starts execution. After submission, Argo automatically assigns a unique name to the workflow instance and begins scheduling tasks.

2. Accessing Argo Web UI

To view the workflow in the browser, expose the Argo server using port forwarding:

```bash
kubectl -n argo port-forward svc/argo-server 2746:2746
```

As part of the pipeline design, a parameterization mechanism was implemented to allow full control over workflow execution without modifying the YAML code. By using the arguments section in Argo Workflows, the system was transformed into a configuration-driven pipeline, where users can dynamically control execution parameters directly from the Argo UI.

## Datasets

1. Credit Card Fraud (150MB)

dataset-url: https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv
target-column: Class

2. Chicago Crime Reports (1.8GB)

dataset-url: https://data.cityofchicago.org/api/views/ijzp-q8t2/rows.csv?accessType=DOWNLOAD
target-column: Arrest

## Benchmark
