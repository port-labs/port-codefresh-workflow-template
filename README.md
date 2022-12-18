# Port Codefresh workflow template

This repository contains:

- The code for a docker image that can be used during an ArgoCD Workflow (or Codefresh workflow) to get an Entity or Upsert an Entity inside Port;
- A Workflow template definition that can be used with the docker image;
- The required service account, role and role binding definitions required to use the workflow template;
- An example secret definition to provide a `CLIENT_ID` and `CLIENT_SECRET` to use the workflow template;
- Usage examples of the Workflow template as part of the ArgoCD workflow process.

The docker image is hosted and managed by Port, there is no need to build and host it yourself.

Use the provided examples to integrate Port with your existing ArgoCD and Codefresh CI process.

# Installation and usage

- Copy and commit the [portWorkflowTemplate.yml](./portWorkflowTemplate.yml) file to your Codefresh git source;
- Add the required service account, cluster role and role binding to your codefresh runtime namespace by using the command: `kubectl apply -f rbac.yml -n YOUR_NAMESPACE`;
- Add a secret to your cluster containing your `PORT_CLIENT_ID` and `PORT_CLIENT_SECRET` after encoding them in base64;
- Use either the `entity-get` or `entity-upsert` template as shown in the [examples](./examples/).

# Available templates

## get-entity

The `get-entity` template gets an existing Port Entity as part of the workflow, and exposes its values as outputs for later workflow steps.

### Inputs

- `PORT_CREDENTIALS_SECRET` - name of the secret to get the `CLIENT_ID` and `CLIENT_SECRET` from (default: `port-credentials`)
- `PORT_CLIENT_ID_KEY` - key in the secret where the base64 encoded `PORT_CLIENT_ID` is stored (default: `PORT_CLIENT_ID`);
- `PORT_CLIENT_SECRET_KEY` - key in the secret where the base64 encoded `PORT_CLIENT_SECRET` is stored (default `PORT_CLIENT_SECRET`);
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
    - name: PORT_CREDENTIALS_SECRET
       value: "port-credentials"
    - name: PORT_CLIENT_ID_KEY
      value: "PORT_CLIENT_ID"
    - name: PORT_CLIENT_SECRET_KEY
      value: "PORT_CLIENT_SECRET"
    - name: BLUEPRINT_IDENTIFIER
      value: "microservice" # Replace with your target blueprint identifier
    - name: ENTITY_IDENTIFIER
      value: "morp" # Replace with your target entity identifier
```

To access the workflow outputs, use `{{workflow.outputs.parameters.[OUTPUT_PARAM]}}`

## upsert-entity

The `upsert-entity` template creates or updates an entity with the identifier matching the one provided.

### Inputes

- `PORT_CREDENTIALS_SECRET` - name of the secret to get the `CLIENT_ID` and `CLIENT_SECRET` from (default: `port-credentials`)
- `PORT_CLIENT_ID_KEY` - key in the secret where the base64 encoded `PORT_CLIENT_ID` is stored (default: `PORT_CLIENT_ID`);
- `PORT_CLIENT_SECRET_KEY` - key in the secret where the base64 encoded `PORT_CLIENT_SECRET` is stored (default `PORT_CLIENT_SECRET`);
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
    # If you save the CLIENT_ID and CLIENT_SECRET in the same format shown in the portCredentials.yml file, there is no need to provide PORT_CREDENTIALS_SECRET, PORT_CLIENT_ID_KEY, PORT_CLIENT_SECRET_KEY
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
