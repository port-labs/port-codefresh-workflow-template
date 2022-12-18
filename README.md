# Port Codefresh workflow template

This repository is composed of 2 pieces:

1. The code for a docker image that can be used during an ArgoCD Workflow (or Codefresh workflow) to get an Entity or Upsert an Entity inside Port.
2. A Workflow template definition that can be used with the docker image, alongside actual usage examples of the Workflow template as part of the ArgoCD workflow process

The docker image is hosted and managed by Port, there is no need to build and host it yourself.

Use the provided examples to integrate Port with your existing ArgoCD and Codefresh CI process.
