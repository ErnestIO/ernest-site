+++
date = "2016-06-06"
title = "AWS YAML Reference"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "AWS YAML Reference"
  weight = -60
  identifier = "aws-yaml"
  parent = "Amazon Web Services"
+++

# Amazon Web Services YAML Reference

Environments built and managed with Ernest are defined in YAML format.

## Example

```
---
name: demo
datacenter: my-dc
vpc_id: vpc-abcdef01
vpc_subnet: 10.0.0.0/16

networks:
  - name: web
    subnet: 10.0.10.0/24
    public: true
  - name: db
    subnet: 10.0.11.0/24
    public: false
    nat_gateway: db-nat

nat_gateways:
  - name: db-nat
    public_network: web

security_groups:
  - name: web-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 10.0.0.0/16
        protocol: any
        from_port: '0'
        to_port: '65535'
      - ip: 52.211.19.211/32
        protocol: tcp
        from_port: '22'
        to_port: '22'
  - name: db-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 10.0.0.0/16
        protocol: any
        from_port: '0'
        to_port: '65535'
      - ip: 52.211.19.211/32
        protocol: tcp
        from_port: '22'
        to_port: '22'

instances:
  - name: web
    elastic_ip: true
    type: t2.micro
    image: ami-ed82e39e
    network: web
    start_ip: 10.0.10.11
    count: 1
    key_pair: web-key
    security_groups:
      - web-sg

  - name: db
    type: t2.micro
    image: ami-ed82e39e
    network: db
    start_ip: 10.0.11.11
    count: 1
    key_pair: db-key
    security_groups:
      - db-sg

loadbalancers:
  - name: elb-1
    private: false
    networks:
      - web
    instances:
      - web
    listeners:
      - from_port: 80
        to_port: 80
        protocol: http
      - from_port: 443
        to_port: 443
        protocol: https
        ssl_cert: ssl-cert-id
    security_groups:
      - web-sg

s3_buckets:
  - name: bucket-1
    acl: private
    bucket_location: eu-west-1
    grantees:
      - id: foo
        type: emailaddress, id, uri
        permissions: full, read, read-acl, write-acl

route53_zones:
  - name: example.com
    private: false
    records:
      - entry: one.example.com
        type: A
        ttl: 3600
        instances:
          - web-1
      - entry: two.example.com
        type: CNAME
        ttl: 3600
        loadbalancers:
          - elb-1
      - entry: three.example.com
        type: MX
        ttl: 3600
        values:
          - 8.8.8.8
          - 8.8.4.4
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

* **vpc_id**
 * String that defines the ID of the existing VPC the service will use.
 * This field is mandatory, unless **vpc_subnet** is present.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

* **vpc_subnet**
 * String that defines the subnet of the VPC that will be created for the service to use.
 * This field is mandatory, unless **vpc_id** is present.
 * This field cannot be null or empty.
 * The value of this field must 50 characters maximum.

### Networking

```
networks:
  - name: web
    subnet: 10.0.10.0/24
    public: true
  - name: db
    subnet: 10.0.11.0/24
    public: false
    nat_gateway: db-nat

nat_gateways:
  - name: db-nat
    public_network: web

```

Networking supports the following fields:

**networks**

A network is a subnet that our instances can connect to. This is what allows us to network/isolate VM’s together.

* **name**
 * String that defines the name of the subnet to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

* **subnet**
 * String that defines the IP address range of the subnet.
 * It must follow CIDR notation as described at: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique for this user & all the networks on the manifest.

* **public**
 * String that defines if the subnet will be public or private.
 * This field is optional.
 * Values can be: “true“ or “false“.
 * This field will default to “false“.

* **nat_gateway**
 * String that defines the name of the NAT gateway to use for this subnet.
 * This field is optional.
 * This field can be empty.

**nat_gateways**

NAT gateways enable instances in a private subnet to connect to the Internet or other AWS services, but prevent the Internet from initiating a connection with those instances.

* **name**
 * String that defines the name of the NAT gateway to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

* **public_network**
 * String that defines the name of the public network the NAT gateway will reside.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

### Security Groups

```
security_groups:
  - name: web-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 10.0.0.0/16
        protocol: any
        from_port: '0'
        to_port: '65535'

```

Security Groups support the following fields:

* **name**
 * String that defines the name of the security group to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field must be 50 characters maximum.

**egress**

Security group egress rules.

* **ip**
 * String that defines the IP address range for this rule.
 * It must follow CIDR notation as described at: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
 * This field is mandatory.
 * This field cannot be null or empty.

* **protocol**
 * String that defines the protocol for this rule.
 * This field is mandatory.
 * This field cannot be null or empty.
 * Values can be: tcp | udp | icmp | any

* **from_port**
 * Starting port-range number.
 * This field is mandatory.
 * Values can be: 0 - 65535.

* **to_port**
 * Ending port-range number.
 * This field is mandatory.
 * Values can be: 0 - 65535.

**ingress**

Security group ingress rules.

* **ip**
 * String that defines the IP address range for this rule.
 * It must follow CIDR notation as described at: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
 * This field is mandatory.
 * This field cannot be null or empty.

* **protocol**
 * String that defines the protocol for this rule.
 * This field is mandatory.
 * This field cannot be null or empty.
 * Values can be: tcp | udp | icmp | any

* **from_port**
 * Starting port-range number.
 * This field is mandatory.
 * Values can be: 0 - 65535.

* **to_port**
 * Ending port-range number.
 * This field is mandatory.
 * Values can be: 0 - 65535.

### Instances

```
instances:
  - name: web
    elastic_ip: true
    type: t2.micro
    image: ami-ed82e39e
    network: web
    start_ip: 10.0.10.11
    count: 1
    key_pair: web-key
    security_groups:
      - web-sg

```

Instances support the following fields:

* **name**
 * String that defines the name of the instance to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user & manifest.
 * The value of this field must be 50 characters maximum.

* **elastic_ip**
 * String that defines if the instance will be assigned an elastic IP.
 * This field is optional.
 * Values can be: “true“ or “false“.
 * This field will default to “false“.

* **type**
 * String that defines the instance size.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be a valid AWS EC2 size.

* **image**
 * String that defines the name of an AMI to install.
 * This field is mandatory.
 * This field cannot be null or empty.

* **network:**
 * String that defines the name of a network to attach this instance.
 * This field is mandatory.
 * This field cannot be null or empty.
 * The value of this field should exist on the networks section previously specified.

* **start_ip:**
 * String that defines the starting IP to allocate the VMs to be built.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be a valid IP.
 * This IP should belong to the network already defined on the instance.

* **count**
 * Integer that defines the number of instances to be created.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be greater or equal that 1

* **key_pair**
 * String that specifies the key pair to be used to login to the instances.
 * This field is optional.
 * This field cannot be null or empty.

* **security_groups**
 * Array that contains security groups that will be applied to the instances.
 * This field is optional.
 * This field can be empty.

### Load Balancers

```
loadbalancers:
  - name: elb-1
    private: false
    subnets:
      - web-subnet
    instances:
      - web
    listeners:
      - from_port: 80
        to_port: 80
        protocol: http
      - from_port: 443
        to_port: 443
        protocol: https
        ssl_cert: ssl-cert-id
    security_groups:
      - web-sg

```
Loadbalancers support the following fields:

* **name**
 * String that defines the name of the instance to build.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user &amp; manifest.
 * The value of this field must be 50 characters maximum.

* **private**
 * Boolean that defines if the loadbalancer will be private or public.
 * This field is optional.
 * Values can be: “true“ or “false“.
 * This field will default to “false“.

 * **networks**
  * Array of String's that defines which public network/subnet to attach to the ELB.
  * This field is mandatory if private is set to false.
  * This field cannot be null or empty.
  * This field must specify a network/subnet that exists on the yaml.
  * Only one subnet can be deployed per availablilty zone. It is advised that you specify an AZ when defining a network/subnet.

* **instances**
 * Array of String's that defines which group of instances to add to the ELB.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must specify an instance group that exists on the yaml.

* **security_groups**
 * Array of String's that defines which security groups to apply to the ELB.
 * This field not is mandatory.
 * This field must specify a security group that exists on the yaml.

**listeners**

ELB Listeners.

* **protocol**
 * String that defines the protocol for this rule.
 * This field is mandatory.
 * This field cannot be null or empty.
 * Values can be: http | https | ssl | tcp
 * If https or ssl is specified, an ssl_cert must be specified.

* **from_port**
 * Starting port-range number.
 * This field is mandatory.
 * Values can be: 1 - 65535.

* **to_port**
 * Ending port-range number.
 * This field is mandatory.
 * Values can be: 1 - 65535.

* **ssl_cert**
 * The ssl certificate ID of a valid AWS Certificate Manager (ACM) cert.
 * This field is mandatory only if protocol is set to https or ssl.


### S3 Buckets

```
s3_buckets:
  - name: bucket-1
    acl: private
    bucket_location: eu-west-1
    grantees:
      - id: foo
        type: emailaddress, id, uri
        permissions: full, read, read-acl, write-acl
```

S3 support the following fields:

* **name**
 * (string) name of the bucket.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user &amp; manifest.
 * The value of this field must be 50 characters maximum.

* **acl**
 * (string) the canned ACL to apply to the bucket.
 * The value of this field can be private, public-read, public-read-write
 * or authenticated-read

* **bucket_location**
 * (string) the location constraint
 * The value of this field can be EU, eu-west-1, us-west-1, us-west-2,
 * ap-south-1, ap-southeast-2, ap-northeast-1, sa-east-1, cn-north-1 or
 * eu-central-1

* **grantees**
 * (collection) List of permissions on the bucket.

* **id**
 * (string) Grantee name

* **type**
 * (string) Grantee type
 * Possible values : email address, id or uri

* **permissions**  
 * (string) Permissions for this grantee on the bucket
 * Possible values: full, read, read-acl and write-acl



 ### Route53 Zones

 ```
 route53_zones:
   - name: example.com
     private: false
     records:
       - entry: one.example.com
         type: A
         ttl: 3600
         values:
           - 8.8.8.8
 ```

 S3 support the following fields:

 * **name**
  * (string) The domain name of the zone.
  * This field is mandatory.
  * This field cannot be null or empty.
  * This field must be unique by user &amp; manifest.

 * **private**
  * (boolean) sets the zone to be private hosted zone.
  * The value of this field can be true or false. Default is false.

**records**

The collection of DNS entries you want to host.

 * **entry**
  * (string) the full domain record entry
  * The value of this field should be an F.Q.D.N (fully qualified domain name)

 * **Type**
 * (string) the full domain record entry
 * The value of this field should be one of 'A', 'AAAA', 'CNAME', 'MX', 'PTR', 'TXT', 'SRV', 'SPF', 'NAPTR', 'NS', 'SOA'

 * **TTL**
 * (int) the expiration period of the entry.
 * The value of this field should be greater than 0

 * **values**
  * Array of strings. The values the entry will return
  * These values must be valid for the type of record you have selected.

 * **Loadbalancers**
  * Array of strings. The loadbalancers you wish the entry to resolve to.
  * The type of record must be set to CNAME
  * You must specify only loadbalancers or instances, not both.

 * **instances**
  * Array of strings. The loadbalancers you wish the entry to resolve to.
  * The type of record must be set to A
  * This must target the individual instance, including the instances number, i.e. web-1
  * You must specify only loadbalancers or instances, not both.
