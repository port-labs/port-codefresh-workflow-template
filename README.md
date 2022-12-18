# Port Codefresh workflow template

This repository is composed of several pieces:

1. The code for a docker image that can be used during an ArgoCD Workflow (or Codefresh workflow) to get an Entity or Upsert an Entity inside Port;
2. A Workflow template definition that can be used with the docker image;
3. Usage examples of the Workflow template as part of the ArgoCD workflow process.

The docker image is hosted and managed by Port, there is no need to build and host it yourself.

Use the provided examples to integrate Port with your existing ArgoCD and Codefresh CI process.

# Installation and usage

- Copy and commit the [portWorkflowTemplate.yml](./portWorkflowTemplate.yml) file to your Codefresh git source.
- Add the required service account, cluster role and role binding to your codefresh runtime namespace by using the command: `kubectl apply -f rbac.yml -n YOUR_NAMESPACE`
- Use either the `entity-get` or `entity-upsert` template as shown in the [examples](./examples/)

# Available templates

## get-entity

The `get-entity` template gets an existing Port Entity as part of the workflow, and exposes its values as outputs for later workflow steps.

### Inputs

- `PORT_CLIENT_ID` - a Port client ID;
- `PORT_CLIENT_SECRET` - a Port client secret;
- `BLUEPRINT_IDENTIFIER` - identifier of the blueprint the target entity is from;
- `ENTITY_IDENTIFIER` - identifier of the target entity.

### Outputs

- `PORT_COMPLETE_ENTITY` - complete entity JSON;
- `PORT_BLUEPRINT_IDENTIFIER` - identifier of the blueprint the target entity is from;
- `PORT_ENTITY_IDENTIFIER` - identifier of the target entity;
- `PORT_ENTITY_TITLE` - title of the entity;
- `PORT_ENTITY_PROPERTIES` - all properties of the entity in JSON format;
- `PORT_ENTITY_RELATIONS` - all relations of the entity in JSON format;

### Example usage

```yaml
- name: entity-get
  templateRef:
    name: port
    template: entity-get
  arguments:
    parameters:
    - name: PORT_CLIENT_ID
       value: "YOUR_CLIENT_ID"
    - name: PORT_CLIENT_SECRET
      value: "YOUR_CLIENT_SECRET"
    - name: BLUEPRINT_IDENTIFIER
      value: "microservice" # Replace with your target blueprint identifier
    - name: ENTITY_IDENTIFIER
      value: "morp" # Replace with your target entity identifier
```

To access the workflow outputs, use `{{workflow.outputs.parameters.[OUTPUT_PARAM]}}`

## upsert-entity

The `upsert-entity` template creates or updates an entity with the identifier matching the one provided.

### Inputes

- `PORT_CLIENT_ID` - a Port client ID;
- `PORT_CLIENT_SECRET` - a Port client secret;
- `BLUEPRINT_IDENTIFIER` - identifier of the blueprint to create an entity of;
- `ENTITY_IDENTIFIER` - identifier of the new (or existing) entity. Leave empty to get an auto-generated identifier;
- `ENTITY_TITLE` - Title of the new (or existing) entity;
- `ENTITY_TEAM` - Team of the new (or existing) entity;
- `ENTITY_ICON` - Icon of the new (or existing) entity;
- `ENTITY_PROPERTIES` - Properties of the new (or existing) entity;
- `ENTITY_RELATIONS` - Relations of the new (or existing) entity.

### Outputs

- `ENTITY_IDENTIFIER` - identifier of the new (or existing) entity.

### Example usage

```yaml
- name: entity-upsert
  templateRef:
    name: port
    template: entity-upsert
  arguments:
    parameters:
    - name: PORT_CLIENT_ID
      value: "YOUR_CLIENT_ID"
    - name: PORT_CLIENT_SECRET
      value: "YOUR_CLIENT_SECRET"
    - name: BLUEPRINT_IDENTIFIER
      value: "microservice"
    - name: ENTITY_IDENTIFIER
      value: "morp"
    - name: ENTITY_TITLE
      value: "Morp-Argo"
    - name: ENTITY_PROPERTIES
      value: |
      {
          "region": "eu-west-4"
      }
```

To access the workflow output, use `{{workflow.outputs.parameters.ENTITY_IDENTIFIER}}`
