+++
date = "2016-10-24"
title = "Developer documentation - Architecture overview"
category = "developer"
showChildren=true
[menu.developer]
  name = "Overview"
  weight = -100
  identifier = "architecture-overview"
  parent = "Architecture"
+++

# Architecture overview

Ernest is based on a microservice oriented architecture. The majority of the services are built using Go, however we still need to maintain some jruby based in order to interact with VCloud.


## Service Structure

These microservices can be categorized into different families:

### Public
This family holds every service with a public or private interface intended to be used by the end user.

- [Api Gateway]({{< relref "api-gateway.md" >}})
- [Monit]({{< relref "monit.md" >}})
- [Logger]({{< relref "logger.md" >}})

### Data Stores
They're a crud over nats interface to interact with persistent data

- [Data stores]({{< relref "stores.md" >}})

### Definition Mappers
Map the input yaml on a valid internal definition

- [Definition mappers]({{< relref "mappers.md" >}})

### Workflow manager
It takes care of processing the necessary steps to build a service.

- [Workflow manager]({{< relref "workflow-manager.md" >}})

### Builders
Builders are processing lists of components

- [Builders]({{< relref "builders.md" >}})

### Adapters
Adapters are processing a single component.

- [Adapters]({{< relref "adapters.md" >}})

### Connectors
They're connecting ernest with third party providers in order to process a component.

- [Connectors]({{< relref "connectors.md" >}})


Additionally we use to group these families on

- Frontend (Public + Stores)
- Backend (Mappers + Core + Builders + Adapters + Connectors)
