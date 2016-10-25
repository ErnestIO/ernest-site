+++
date = "2016-10-24"
title = "Developer documentation - Naming conventions"
category = "documentation"
showChildren=true
[menu.developer]
  name = "Naming conventions"
  weight = -100
  identifier = "naming-conventions"
  parent = "Architecture"
+++

# Naming conventions

In order to keep it clean there are some naming conventions you need to follow in order to understand ernest

## Service Naming

Every service family is following an easy to understand naming conventions.

### Mappers

Mappers will be named following the structure *<provider>-definition-mapper* where provider is the cloud service it will be mapping AWS, VCloud ...


### Adapters

We base the adapters naming on the component they're representing *<component>-adapter*. A component can be an instance, a network, a s3 bucket...

### Connectors

A connector name will look like *<component>-<action>-<provider>-component*.

It is basically composed by some variable parts:
- component : the component it represents: instances, networks, routers...
- action : the action will implement: create, delete, update. Additionally you will find the action "all", which will represent is implementing all possible actions.
- provider : the provider is connecting to. AWS, VCloud...

## Messages naming

All services on ernest are comunicating through nats messages, there is a naming conventions for any message on the platform, and you can find it in diferent forms:

#### <component>s.<action>

These messages are representing the comunication between the workflow manager and the builders, and basically they're managing a list of components to be processed.

#### <component>.<action>

A single component to be processed, these messages are managed by builders and adapters.

#### <component>.<action>.<provider>

They are sending the necessary info from the adapter to be processed by a specific connnector.

All these messages are replied with an appended ".done" or ".error" word to the subject, so the sender will know if the action was a success or a failure.
