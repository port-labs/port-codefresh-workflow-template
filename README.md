# Port Codefresh workflow template

This repository is composed of several pieces:

1. The code for a docker image that can be used during an ArgoCD Workflow (or Codefresh workflow) to get an Entity or Upsert an Entity inside Port;
2. A Workflow template definition that can be used with the docker image;
3. Usage examples of the Workflow template as part of the ArgoCD workflow process.

The docker image is hosted and managed by Port, there is no need to build and host it yourself.

Use the provided examples to integrate Port with your existing ArgoCD and Codefresh CI process.

# Installation

- Copy and commit the [portWorkflowTemplate.yml](./portWorkflowTemplate.yml) file to your Codefresh git source.
- Add the required service account, cluster role and role binding to your codefresh runtime namespace by using the command: `kubectl apply -f rbac.yml -n YOUR_NAMESPACE`
- Use either the `entity-get` or `entity-upsert` template as shown in the [examples](./examples/)
