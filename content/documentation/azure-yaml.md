+++
date = "2016-06-06"
title = "Azure YAML Reference"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "Azure YAML Reference"
  weight = -60
  identifier = "azure-yaml"
  parent = "Azure"
+++

# Azure YAML Reference

Environments built and managed with Ernest are defined in YAML format.

## Example

```
---
name: demo
datacenter: my-dc
```

## Field Reference

### Service Options

```
name: demo
datacenter: my-dc
vpc_id: vpc-abcdef01
vpc_subnet: 10.0.0.0/16

```

Service Options support the following fields:

* **name**
 * String that defines the name of the service to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

* **datacenter**
 * String that defines the name of the datacenter where the service is built.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must 50 characters maximum.

### Virtual Networks

```
virtual_networks:
  - name: web
    address_space: 
      - "10.1.2.0/24"
    location = "West US"
    subnet:   
      name: subnet1
      address: 10.1.2.0/25

```

Networking supports the following fields:

**virtual networks**

An Azure virtual network (VNet) is a representation of your own network in the cloud. It is a logical isolation of the Azure cloud dedicated to your subscription. [+Info](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)

* **name**
 * String that defines the name of the virtual network
 * This field is mandatory.
 * This field cannot be null or empty.

* **address_space**
 * Strings array defining the multiple address spaces used by the virtual network.
 * It must follow CIDR notation as described [here](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
 * This field is mandatory.
 * This field cannot be null or empty.

* **location**
 * String defining the location/region where the virtual network will be created.
 * This field is mandatory.
 * This field cannot be null or empty.

* **subnet:name**
 * String defining the subnet name to be used
 * This field is mandatory.
 * This field can be empty.

* **subnet:address**
 * String defining the subnet address prefix to use for the subnet
 * This field is mandatory.
 * This field can be empty.


