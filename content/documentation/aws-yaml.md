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
  - name: db-standby
    subnet: 10.0.12.0/24
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
      - entry: db.example.com
        type: CNAME
        ttl: 3600
        rds_clusters:
          - rds-aurora

rds_clusters:
  - name: rds-aurora
    engine: aurora
    port: 3306
    networks:
      - db
      - db-standby
    security_groups:
      - db-sg-1
    database_name: test
    database_username: test
    database_password: testpass-2

rds_instances:
  - name: rds-test-1
    cluster: rds-test
    size: db.r3.xlarge
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

### RDS Clusters


```
rds_clusters:
  - name: aurora-test
    engine: aurora
    engine_version: 1.9
    public: false
    port: 3306
    availablily_zones:
      - eu-west-1a
      - eu-west-1b
      - eu-west-1c
    security_groups:
      - sg-1
    networks:
      - db-nw
    database_name: aurora-test
    database_username: username
    database_password: password
    backups:
      window: Mon:21:30-Mon:22:00
      rentention_period: 1
    maintenance_window: Mon:21:30-Mon:22:00
    final_snapshot: true

```
RDS Clusters support the following fields:

* **name**
 * (string) The name of the rds cluster.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user &amp; manifest.

* **engine**
 * (string) The engine type of the rds cluster.
 * This field is mandatory.
 * This field cannot be null or empty.
 * Currently only 'aurora' is supported as a value.

* **engine_version**
 * (string) The engine_version of the rds cluster.
 * This field is not mandatory.

* **port**
 * (int) The port exposed by the rds cluster.
 * This field not is mandatory.
 * This field must be between 1150 - 65535 (default: 3306).

* **public**
 * (boolean) Sets the rds cluster to be publicly accessible.
 * This field not is mandatory.
 * The value of this field can be true or alse. Default is false.

* **availability_zones**
 * Array of strings. The availability zones to deploy the rds cluster to.
 * This field not is mandatory.
 * The availability zone must be in the same region as the vpc it is deployed to.
 * If the rds cluster is set to private, there must be networks specified for each availability zone.

* **security_groups**
 * Array of String's that defines which security groups to apply to the rds cluster.
 * This field not is mandatory.
 * This field must specify a security group that exists on the yaml.

* **networks**
 * Array of String's that defines which networks to expose the rds cluster to.
 * This field not is mandatory (if public is set to true).
 * This field must specify a security group that exists on the yaml.

* **database_name**
 * (string) The database name of the rds cluster.
 * This field is mandatory.
 * This field cannot be null or empty.

* **database_username**
 * (string) The database username of the rds cluster.
 * This field is mandatory.
 * This field cannot be null or empty.

* **database_username**
 * (string) The database password of the rds cluster.
 * This field is mandatory.
 * This field cannot be null or empty.

* **maintenance_window**
 * (string) The time period at which any maintenance tasks will occur.
 * This field is not mandatory.
 * Format must be a range in the structure of DDD:HH::MM-DDD:HH::MM (Day:Hour:Minute)

* **replication_source**
 * (string) The target rds cluster or mysql instance to replicate from.
 * Must be a valid ARN (Amazon Resource Name).
 * http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html

* **final_snapshot**
 * (boolean) Performs a final snapshot when the rds cluster is deleted.
 * This field not is mandatory.
 * The value of this field can be true or alse. Default is false.

**backups**

Backup configuration.

* **window**
 * (string) The time period at which backups will occur.
 * This field is not mandatory.
 * Format must be a range in the structure of DDD:HH::MM-DDD:HH::MM (Day:Hour:Minute)

* **retention**
 * (int) The amount of time in days that backups will be retained for.
 * This field is not mandatory.
 * Must be between 1 - 35 days


### RDS Instances

```
rds_instances:
  - name: rds-test-1
    size: db.r3.xlarge
    engine: mysql
    engine_version: 5.7
    port: 3306
    public: true
    multi_az: true
    storage:
      type: gp2
      size: 100
      iops: 5000
    auto_upgrade: true
    final_snapshot: true
    license: bring-your-own-license
    database_name: aurora-test
    database_username: username
    database_password: password
    backups:
      window: Mon:21:30-Mon:22:00
      rentention_period: 1
    maintenance_window: Mon:21:30-Mon:22:00
    final_snapshot: true
```


RDS Instances support the following fields:

* **name**
 * (string) The name of the rds instance.
 * This field is mandatory.
 * This field cannot be null or empty.
 * This field must be unique by user &amp; manifest.

* **engine**
 * (string) The engine type of the rds instance.
 * This field is mandatory.
 * This field cannot be null or empty.
 * Currently only 'aurora' is supported as a value.

* **engine_version**
 * (string) The engine_version of the rds instance.
 * This field is not mandatory.

* **port**
 * (int) The port exposed by the rds instance.
 * This field not is mandatory.
 * This field must be between 1150 - 65535 (default: 3306).

* **public**
 * (boolean) Sets the rds instance to be publicly accessible.
 * This field not is mandatory.
 * The value of this field can be true or alse. Default is false.

* **multi_az**
 * (boolean) Deploys a synchronous standby instance in a different availability zone
 * Cannot be used in conjunction with the 'availability_zone' parameter
 * This field not is mandatory.
 * The value of this field can be true or alse. Default is false.

* **availability_zones**
 * Array of strings. The availability zones to deploy the rds instance to.
 * This field not is mandatory.
 * The availability zone must be in the same region as the vpc it is deployed to.
 * If the rds instance is set to private, there must be networks specified for each availability zone.

* **security_groups**
 * Array of String's that defines which security groups to apply to the rds instance.
 * This field not is mandatory.
 * This field must specify a security group that exists on the yaml.

* **networks**
 * Array of String's that defines which networks to expose the rds instance to.
 * This field not is mandatory (if public is set to true).
 * This field must specify a security group that exists on the yaml.

* **database_name**
 * (string) The database name of the rds instance.
 * This field is mandatory.
 * This field cannot be null or empty.

* **database_username**
 * (string) The database username of the rds instance.
 * This field is mandatory.
 * This field cannot be null or empty.

* **database_username**
 * (string) The database password of the rds instance.
 * This field is mandatory.
 * This field cannot be null or empty.

* **maintenance_window**
 * (string) The time period at which any maintenance tasks will occur.
 * This field is not mandatory.
 * Format must be a range in the structure of DDD:HH::MM-DDD:HH::MM (Day:Hour:Minute)

* **promotion_tier**
 * (int) specifies the order in which an aurora replica is promoted to a primary.
 * This field is not mandatory.
 * Must be between 1 - 15.
 * Default is 1.

* **auto_upgrade**
 * (boolean) Performs automatic upgrades on the database (minor versions only)
 * This field not is mandatory.
 * The value of this field can be true or alse. Default is false.

* **replication_source**
 * (string) The target an rds instance to replicate from.
 * Must be a valid ARN (Amazon Resource Name) or instance name (if in the same region).
 * http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html

* **final_snapshot**
 * (boolean) Performs a final snapshot when the rds instance is deleted.
 * This field not is mandatory.
 * The value of this field can be true or alse. Default is false.


* **license**
 * (string) The type of license you want to use with the database
 * This field is not mandatory.
 * Must be one of 'license-included', 'bring-your-own-license' or 'general-public-license'

**storage**

Storage configuration. Only configurable when using a non-aurora engine.

* **type**
 * (string) The type of storage you want to use.
 * This field is not mandatory.
 * Must be one of 'gp2' (general purpose SSD), 'io1' (performance optimised SSD) or 'standard' (magnetic disk)
 * If io1 is specified, you must also set the 'iops' parameter

* **size**
 * (int) The amount of storage (GB) that you want to use.
 * This field is not mandatory.
 * Must be a value between 5 - 6144.
 * Minimum size can vary between engine types.

* **iops**
 * (int) The amount of iops that you want the database to be limited to.
 * This field is mandatory if storage type is 'io1'.
 * This field must only be used in conjunction with 'io1' as a storage type.
 * Must be a multiple between 3 and 10 of the storage amount.
 * Must also be an integer multiple of 1000.

**backups**

Backup configuration.

* **window**
 * (string) The time period at which backups will occur.
 * This field is not mandatory.
 * Format must be a range in the structure of DDD:HH::MM-DDD:HH::MM (Day:Hour:Minute)

* **retention**
 * (int) The amount of time in days that backups will be retained for.
 * This field is not mandatory.
 * Must be between 1 - 35 days
