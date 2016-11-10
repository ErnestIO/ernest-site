+++
date = "2016-10-24"
title = "Developer documentation - Data stores"
category = "developer"
showChildren=true
[menu.developer]
  name = "Data stores"
  weight = -98
  identifier = "stores"
  parent = "Architecture"
+++

# Data stores

Microservices on this family are an inteface to interact with persistent data. So you can query stored data through nats with messages like:

- entity.get
- entity.find
- entity.del
- entity.set

Basically you'll find a microservice per each entity on the system, which actually are:

#### datacenter-store

Implements a nats interface to operate with datacenter stored data

#### user-store

Will open a nats interface to operate with user stored data. Additionally will manage all user sensible data encryption.

#### service-store

Implements a nats interface to operate with service stored data

#### config-store

Implements a nats interface to operate with system configuration, this configuration is based on a configuration file deployed on ernest creation.

#### group-store

Implements a nats interface to operate with groups stored data. System authorization levels are based on this groups

