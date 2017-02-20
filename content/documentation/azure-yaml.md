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


## Resource groups

Every single azure resource is part of a resource group, so, in order to create any resource, you'll first need a resource group. 
Resource groups are pretty simple, they just need 2 required fields,name and location. This will allow multi-location definitions environments support.

```
resource_group:
  - name: web
    location: westus
    tags:
      - environment: production
```

Resource groups are defined by the following fields:

**name**
  * String defining the name of the resource group
  * Must be unique on all your azure subscription.

**location**
  * String defining the location where the resource group should be created.
  * All resources on this resource group will inherit this location
  * Check the list of all valid Azure locations [here](https://azure.microsoft.com/en-us/regions/)

**tags**
  * A key-value map of tags to assign to the resource group

Resource groups are a container to have together multiple resources, lets see below which ones are supported:

### Virtual Networks

```

    virtual_networks:
      - name: vm_test                           # Required: The name of the virtual network. Changing this forces a new resource to be created.
        address_spaces:                         # Required : The address space that is used the virtual network. You can supply more than one address space. Changing this forces a new resource to be created.
          - 10.0.0.0/16
        dns_servers:                            # Optional : List of IP addresses of DNS servers
          - 8.8.8.8
          - 4.4.4.4
        subnets:                                # Optional : Can be specified multiple times to define multiple subnets.
          - name: sub_test                        # Required : The name of the subnet.
            address_prefix: 10.0.1.0/24           # Required : The address prefix to use for the subnet.
            security_group: my_security_group     # Optional : The Network Security Group to associate with the subnet. (Referenced by name.
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


