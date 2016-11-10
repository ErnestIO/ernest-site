+++
date = "2016-10-24"
title = "Developer documentation - Workflow manager"
category = "developer"
showChildren=true
[menu.developer]
  name = "Workflow manager"
  weight = -94
  identifier = "workflow-manager"
  parent = "Architecture"
+++

# Workflow manager

Workflow manager is a service to process grouped lists of components. It actually works on top of nats.io. Workflow manager provides serveral key features:

- **Adaptable workflow definition** : Workflow definition is received as part of the entry point, so it is externally defined.
- **Templating** : You can use the templating system to fill some component details with other component properties.
- **External persistence** : Workflow manager will call an external service to persist the current definition.



## Quick start

```
git clone git@github.com:ernestio/workflow-manager.git
make deps && make install
workflow-manager
```



## Dependencies

As workflow-manager does not provide any persistence system it directly depends on [service-store](https://github.com/ErnestIO/service-store), and its communcation is through nats.io.



## Input (definition)

The input definition is basically a json input with the following structure:
```
{
    "id": "test-generated-id",
    "workflow": {
			"arcs": [{
				"from": "created",
				"to": "started",
				"event": "service.create"
			}, {
				"from": "started",
				"to": "creating_components",
				"event": "components.create"
			}, {
				"from": "creating_components",
				"to": "components_created",
				"event": "components.create.done"
			}, {
				"from": "components_created",
				"to": "updating_components",
				"event": "components.update"
			}, {
				"from": "updating_components",
				"to": "components_updated",
				"event": "components.update.done"
			}, {
				"from": "components_updated",
				"to": "deleting_components",
				"event": "components.delete"
			}, {
				"from": "deleting_components",
				"to": "components_deleted",
				"event": "components.delete.done"
			}, {
				"from": "components_deleted",
				"to": "done",
				"event": "service.create.done"
			}, {
				"from": "pre-failed",
				"to": "failed",
				"event": "to_error"
			}, {
				"from": "failed",
				"to": "errored",
				"event": "service.create.error"
			}]
    },
    "components": {
      "status": "",
      "started": "",
      "finished": "",
      "items": [{
        "service": "test",
        "type": "vcloud",
        "name": "existing",
        "field": "existing" 
      }]
    },
    "components_to_create": {
      "status": "",
      "started": "",
      "finished": "",
      "items": [{
        "service": "test",
        "type": "vcloud",
        "name": "added",
        "field": "created" 
      },{
        "service": "test",
        "type": "vcloud",
        "name": "updated",
        "field": "created_to_be_updated" 
      }]
    },
    "components_to_update": {
      "status": "",
      "started": "",
      "finished": "",
      "items": [{
        "service": "test",
        "type": "vcloud",
        "name": "updated",
        "field": "updated" 
      }]
    },
    "components_to_delete": {
      "status": "",
      "started": "",
      "finished": "",
      "items": [{
        "service": "test",
        "type": "vcloud",
        "name": "existing"
      }]
    }
}
```

You can change add / remove as many component batches as you want, they will need to be represented as part of the workflow too.

In order to build workflows, you can have a look at [workflow library](https://github.com/r3labs/workflow).

Workflow-manager will send a **components.verb** for each transition you've defined on your workflow, and will wait for **component.verb.status**, where status can be done or error.

You will find a more up to date documentation on [the repo README](https://github.com/ernestio/workflow-manager/blob/develop/README.md)
