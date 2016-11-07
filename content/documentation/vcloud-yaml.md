+++
date = "2016-06-06"
title = "vCloud YAML Reference"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "vCloud YAML Reference"
  weight = -60
  identifier = "vcloud-yaml"
  parent = "vCloud Director"
+++

# vCloud Director YAML Reference

Environments built and managed with Ernest are defined in YAML format.

## Example

```
---
name: demo
datacenter: r3-jreid2
bootstrapping: salt
service_ip: 195.3.186.44
ernest_ip:
  - 31.210.240.161

routers: 
  - name: demo
    rules:
    - name: in_out_any
      source: internal
      from_port: any
      destination: external
      to_port: any
      protocol: any
      action: allow

    - name: out_in_80
      source: any
      from_port: any
      destination: internal
      to_port: '80'
      protocol: tcp
      action: allow

    networks:
      - name: web
        subnet: 10.1.0.0/24
        dns:
          - 8.8.8.8
          - 8.8.4.4

    port_forwarding:
      - source: 195.3.186.44
        from_port: '80'
        to_port: '80'
        destination: 10.1.0.11

instances:
  - name: web
    image: r3/ubuntu-1404
    cpus: 1
    memory: 1GB
    disks:
     - 10GB
    root_disk: 32GB
    count: 1
    networks:
      name: web
      start_ip: 10.1.0.11
    provisioner:
      - exec:
        - 'sudo apt-get update'
        - 'sudo apt-get install apache2 -y'
      - shell:
        - #!/bin/sh
        - if [ x$1 == x"postcustomization" ]; then
        - yum -y install httpd
        - service httpd start
        - fi 
```

## Field Reference

### Service Options

```
name: demo
datacenter: r3-jreid2
bootstrapping: salt
service_ip: 195.3.186.44
ernest_ip:
  - 31.210.240.161
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


* **bootstrapping**
 * String [salt, none] that defines if you’re going to be able to execute commands on the servers
 * This field is optional.
 * This field defaults to none.
 * The value of this is restricted to “salt” or “none”

* **service_ip**
 * String that defines the public ip of the router already created on vcloud
 * This field is optional
 * This field defaults to none.
 * The value of this field is an IP, internally defined as https://golang.org/pkg/net/#IP

* **ernest_ip**
 * Array of IPs that defines the ernest instance public ip, so it will be allowed through the created service firewall rules.
 * The value of this field is an IP, internally defined as https://golang.org/pkg/net/#IP  
 * This field is mandatory only if you’ve defined bootstrapping as “salt”
 * This field defaults to an empty array.

### Networking

```
routers: 
  - name: demo
    rules:
    - name: in_out_any
      source: internal
      from_port: any
      destination: external
      to_port: any
      protocol: any
      action: allow

    - name: out_in_80
      source: any
      from_port: any
      destination: internal
      to_port: '80'
      protocol: tcp
      action: allow

    networks:
      - name: web
        subnet: 10.1.0.0/24
        dns:
          - 8.8.8.8
          - 8.8.4.4

    port_forwarding:
      - source: 195.3.186.44
        from_port: '80'
        to_port: '80'
        destination: 10.1.0.11
```

Networking supports the following fields:

* **name**
 * String that defines the name of the service to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user & manifest.
 * The value of this field can be 50 characters maximum.

**rules**

A firewall rule controls traffic that is allowed to flow between both internal and external routers and networks.

* **name**
 * String that defines the name of the rule.
 * This field is mandatory.
 * This field can’t be null or empty.

* **source**
 * String the source of the network where this firewall acts
 * This field is mandatory.
 * Values can be: external | internal | any | named networks | CIDR

* **from_port**
 * Source port numbers
 * This field is mandatory.
 * Values can be:  an string ( valid port number, 1 - 65535 ) or any

* **destination**
 * String the destination of the network where this firewall acts
 * This field is mandatory.
 * Values can be:  external | internal | any | named networks | CIDR

* **to_port**
 * Destination port numbers
 * This field is mandatory.
 * Values can be:  an string ( valid port number, 1 - 65535 ) or any

* **protocol**
 * String with the protocol we are firewalling
 * This field is mandatory.
 * Values can be: tcp | udp | icmp | any | tcp & udp


**networks**

A network is a virtual network that attaches to a router (in vcloud, other providers will differ). This is what allows us to network/isolate VM’s together.

* **name**
 * String that defines the name of the service to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

* **subnet**
 * String that defines the name of a network to add to this service.
 * It must follow CIDR notation as described at: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing 
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique for this user & all the networks on the manifest.

* **dns**
 * Array of IPs to use as dns servers on this service
 * It must follow CIDR notation as described at: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing 
 * This field is optional.
 * This field can be empty and it will default to ["8.8.8.8", "8.8.4.4"].

**port_forwarding**

A port forward is something that converts translates a request on a port from one IP to another on a different network. Only tcp port forwarding is supported.

* **from_port**
 * Source port numbers
 * Values can be:  an string ( valid port number, 1 - 65535 ) or any

* **destination**
 * String the destination of the IPv4 Address to translate to
 * Values can be: IPv4

* **to_port**
 * Destination port numbers
 * Values can be:  an string ( valid port number, 1 - 65535 ) or any

* **source**
 * String that defines the IP from the provider-specified sub-allocated list of IPs of the router.
 * It must follow CIDR notation as described at: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing 
 * This field is optional.
 * This field can be null or empty, in case a router is created by Ernest it will default to the created one, on the other hand this value will be used.
 * This field must be unique for this user & all the networks on the manifest.

### Instances

```
instances:
  - name: web
    image: r3/ubuntu-1404
    cpus: 1
    memory: 1GB
    disks:
     - 10GB
    root_disk: 32GB
    count: 1
    networks:
      name: web
      start_ip: 10.1.0.11
    provisioner:
      - exec:
        - 'sudo apt-get update'
        - 'sudo apt-get install apache2 -y'
      - shell:
        - #!/bin/sh
        - if [ x$1 == x"postcustomization" ]; then
        - yum -y install httpd
        - service httpd start
        - fi 
```

Instances support the following fields:

* **name**
 * String that defines the name of the instance to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user & manifest.
 * The value of this field must be 50 characters maximum.

* **image**
 * String that defines the name of an image to install on the VM.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field is composed in two parts one for the “catalog” and another one for the “image” separated by a slash “/”
 * Both “catalog” and “image” are strings and can’t be null.

* **cpus**
 * Integer that defines how many CPUs will have the VM.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be greater or equal than 1

* **memory**
 * String that defines the name of a load balancer to add to this service.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must follow the Binary Prefix format: https://en.wikipedia.org/wiki/Binary_prefix#Computer_memory 
 * The possible binary prefixes are MB, GB, TB, PB, YB

* **disks:**
 * A list of sizes of hard disks that belongs to the VM
 * This field can be an empty list
 * This field is a list of strings
 * Each element of the string must follow the Binary Prefix format: https://en.wikipedia.org/wiki/Binary_prefix#Hard_disk_drives 
 * The possible binary prefixes are MB, GB, TB, PB, YB

* **root_disk**
 * String that defines the root disk size of the template the instance is based on.
 * This field is optional.
 * This field cannot be null or empty.
 * The value must be larger than the default root disk size of the template.
 * Each element of the string must follow the Binary Prefix format: https://en.wikipedia.org/wiki/Binary_prefix#Hard_disk_drives 
 * The possible binary prefixes are MB, GB, TB, PB, YB

* **count**
 * Integer that defines the number of VM to be created.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be greater or equal that 1

**networks**
Networks is a map with two fields


* **name**
 * String that defines the name of a network to attach this instance.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field should exist on the networks section previously specified.


* **start_ip**
 * String that defines the starting IP to allocate the VMs to be built.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be a valid IP.
 * This IP should belong to the network already defined on the instance.


**provisioner**

Provisioner defines the available provisioner mechanisms.

* **exec**
 * Array of strings, any command characters must be escaped with backslashes: (RFC7159, Section 7)
 * Each command in the exec array is concatenated together and delimited by a semicolon.
 * This field is optional
 * If specified as a provisioner type, this array field must contain at least one element
 * The specified commands will be executed by the SALT master.

* **shell**
 * Array of strings, any command characters must be escaped with backslashes: (RFC7159, Section 7)
 * Each command in the exec array is concatenated together and delimited by a semicolon.
 * This field is optional
 * If specified as a provisioner type, this array field must contain at least one element
 * The specified commands will be executed by the vCloud guest customization script.