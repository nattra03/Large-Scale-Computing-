## Authors
Natalia Trąba, Patrycja Markiewicz

## Project topic
Argo Workflows: DAG Pipelines on Kubernetes Build and benchmark a multi-stage data or ML 
pipeline using Argo Workflows on Kind, demonstrating DAG vs. step execution models, artifact passing strategies, and retry/fault-tolerance behavior.

## Local Kubernetes cluster setup and installation of Argo Workflows

The goal of this stage is to create a local development environment that simulates a cloud 
computing infrastructure. Within this environment, a workflow orchestration system – Argo Workflows – and an 
object storage system – MinIO – are installed and configured.
This setup enables testing and execution of data pipelines in conditions close to a production environment.

1.

```bash
