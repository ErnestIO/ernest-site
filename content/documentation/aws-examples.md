+++
date = "2016-06-06"
title = "AWS Examples"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "AWS Examples"
  weight = -90
  identifier = "aws-examples"
  parent = "Amazon Web Services"
+++

# Amazon Web Services Examples

## Setup

Before we get started we will need the following information:

* AWS access key
* AWS secret key
* AWS VPC ID
* Ernest IP address
* Ernest username/password

The first step is to set the IP address of Ernest:

```
$ ernest target https://10.50.1.11
Target set

```

Next we login to Ernest:

```
$ ernest login
Username: user1
Password: ******
Welcome back user1

```

Once we have logged in to Ernest we can setup the AWS datacenter and credentials that Ernest will use to create our infrastructure:

```
$ ernest datacenter create aws --region eu-west-1 --secret_access_key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX --access_key_id YYYYYYYYYYYYYYYYYYYY my-dc
Datacenter 'my-dc' successfully created

```

Now that we have our datacenter created in Ernest we can start building stuff.

## Creating a Service

We will create a simple environment with one Ubuntu server, a public IP assigned to it, and the ability to ssh to that server from our IP (52.211.19.211).

Our environment is defined in the following YAML:

```
---
name: demo
datacenter: my-dc
vpc_id: vpc-abcdef01

networks:
  - name: public
    subnet: 10.0.10.0/24
    public: true

security_groups:
  - name: public-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 52.211.19.211/32
        protocol: tcp
        from_port: '22'
        to_port: '22'

instances:
  - name: public
    elastic_ip: true
    type: t2.micro
    image: ami-ed82e39e
    network: public
    start_ip: 10.0.10.11
    count: 1
    key_pair: my-key
    security_groups:
      - public-sg

```

Lets apply our definition:

```
$ ernest service apply demo.yml 
Environment creation requested
Ernest will show you all output from your requested service creation
You can cancel at any moment with Ctrl+C, even the service is still being created, you won't have any output
Starting environment creation

Creating networks:
 - my-dc-demo-public
   IP     : 10.0.10.0/24
   AWS ID : subnet-defabc01
   Status : completed
Networks successfully created

Creating firewalls:
 - my-dc-demo-public-sg
   Status    : completed
Firewalls created

Creating instances:
 - my-dc-demo-public-1
   IP        : 10.0.10.11
   AWS ID    : i-abcdef01abcdef011
   Status    : completed
Instances successfully created

Updating instances:
 - my-dc-demo-public-1
   IP        : 10.0.10.11
   PUBLIC IP : 52.210.179.96
   AWS ID    : i-abcdef01abcdef011
   Status    : completed
Instances successfully updated
SUCCESS: rules successfully applied
Your environment endpoint is: 

```

We can list the services we have built:

```
$ ernest service list
NAME  UPDATED         STATUS  ENDPOINT
demo  2016-09-12 15:57:46.195942 +0000 UTC  done

```

We can see detailed provider-generated information related to our service:

```
$ ernest service info demo
Name : demo
VPC : vpc-abcdef01

ELBs (empty)

Networks:
+---------------------------+-----------------+
|           NAME            |       ID        |
+---------------------------+-----------------+
| my-dc-demo-public         | subnet-defabc01 |
+---------------------------+-----------------+

Instances:
+-----------------------------+---------------------+---------------+------------+
|            NAME             |         ID          |   PUBLIC IP   | PRIVATE IP |
+-----------------------------+---------------------+---------------+------------+
| my-dc-demo-public-1         | i-abcdef01abcdef011 | 52.210.179.96 | 10.0.10.11 |
+-----------------------------+---------------------+---------------+------------+

NAT gateways (empty)

Security groups:
+------------------------------+-------------+
|             NAME             |  GROUP ID   |
+------------------------------+-------------+
| my-dc-demo-public-sg         | sg-01234567 |
+------------------------------+-------------+

```

We can view the history of applies for our service:

```
$ ernest service history demo
NAME  BUILD ID                UPDATED         STATUS
demo  89389b76-cc25-4add-55e5-b7647217b4b1-abf663d6c173d4af98e3ff20bb7e8dde 2016-09-12 15:57:46.195942 +0000 UTC  done

```

For our service we can show the definition applied for a given Build ID:

```
$ ernest service definition demo --build 89389b76-cc25-4add-55e5-b7647217b4b1-abf663d6c173d4af98e3ff20bb7e8dde
---
name: demo
datacenter: my-dc
vpc_id: vpc-abcdef01

networks:
  - name: public
    subnet: 10.0.10.0/24
    public: true

security_groups:
  - name: public-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 52.211.19.211/32
        protocol: tcp
        from_port: '22'
        to_port: '22'

instances:
  - name: public
    elastic_ip: true
    type: t2.micro
    image: ami-ed82e39e
    network: public
    start_ip: 10.0.10.11
    count: 1
    key_pair: my-key
    security_groups:
      - public-sg

```

## Modifying a Service

Lets modify the service we create above. We will add a private network with a server on it and a NAT gateway attached to it.

Our modified YAML file is:

```
---
name: demo
datacenter: my-dc
vpc_id: vpc-abcdef01

networks:
  - name: public
    subnet: 10.0.10.0/24
    public: true
  - name: private
    subnet: 10.0.11.0/24
    public: false
    nat_gateway: private-nat

nat_gateways:
  - name: private-nat
    public_network: public

security_groups:
  - name: public-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 52.211.19.211/32
        protocol: tcp
        from_port: '22'
        to_port: '22'
  - name: private-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 10.0.0.0/16
        protocol: tcp
        from_port: '22'
        to_port: '22'

instances:
  - name: public
    elastic_ip: true
    type: t2.micro
    image: ami-ed82e39e
    network: public
    start_ip: 10.0.10.11
    count: 1
    key_pair: my-key
    security_groups:
      - public-sg

  - name: private
    type: t2.micro
    image: ami-ed82e39e
    network: private
    start_ip: 10.0.11.11
    count: 1
    key_pair: my-key
    security_groups:
      - private-sg

```

When we apply this YAML we can see Ernest make the necessary changes:

```
$ ernest service apply demo.yml 
Environment creation requested
Ernest will show you all output from your requested service creation
You can cancel at any moment with Ctrl+C, even the service is still being created, you won't have any output
Starting environment creation

Creating networks:
 - my-dc-demo-private
   IP     : 10.0.11.0/24
   AWS ID : subnet-defabc02
   Status : completed
Networks successfully created

Creating firewalls:
 - my-dc-demo-private-sg
   Status    : completed
Firewalls created

Creating instances:
 - my-dc-demo-private-1
   IP        : 10.0.11.11
   AWS ID    : i-abcdef01abcdef012
   Status    : completed
Instances successfully created

Updating instances:
 - my-dc-demo-private-1
   IP        : 10.0.11.11
   PUBLIC IP : 
   AWS ID    : i-abcdef01abcdef012
   Status    : completed
Instances successfully updated

Creating nats:
 - my-dc-demo-private-nat
   Status    : completed
Nats created
SUCCESS: rules successfully applied
Your environment endpoint is: 

```

We can see our service in the list, with the most recent update time:

```
$ ernest service list
NAME  UPDATED         STATUS  ENDPOINT
demo  2016-09-12 16:03:40.195942 +0000 UTC  done

```

The service info has also updated with the new information:

```
$ ernest service info demo
Name : demo
VPC : vpc-abcdef01

ELBs (empty)

Networks:
+---------------------------+-----------------+
|           NAME            |       ID        |
+---------------------------+-----------------+
| my-dc-demo-public         | subnet-defabc01 |
| my-dc-demo-private        | subnet-defabc02 |
+---------------------------+-----------------+

Instances:
+-----------------------------+---------------------+---------------+------------+
|            NAME             |         ID          |   PUBLIC IP   | PRIVATE IP |
+-----------------------------+---------------------+---------------+------------+
| my-dc-demo-public-1         | i-abcdef01abcdef011 | 52.210.179.96 | 10.0.10.11 |
| my-dc-demo-private-1        | i-abcdef01abcdef012 |               | 10.0.11.11 |
+-----------------------------+---------------------+---------------+------------+

NAT gateways:
+-------------------------------+-----------------------+
|             NAME              |       GROUP ID        |
+-------------------------------+-----------------------+
| my-dc-demo-private-nat        | nat-abcdef01abcdef013 |
+-------------------------------+-----------------------+

Security groups:
+------------------------------+-------------+
|             NAME             |  GROUP ID   |
+------------------------------+-------------+
| my-dc-demo-public-sg         | sg-01234567 |
| my-dc-demo-private-sg        | sg-89012345 |
+------------------------------+-------------+

```

The history shows both of the applies we have done for this service:

```
$ ernest service history demo
NAME  BUILD ID                UPDATED         STATUS
demo  72c1306c-c6ee-4d9b-4230-617d0e969ea9-abf663d6c173d4af98e3ff20bb7e8dde 2016-09-12 16:03:40.235144 +0000 UTC  done
demo  89389b76-cc25-4add-55e5-b7647217b4b1-abf663d6c173d4af98e3ff20bb7e8dde 2016-09-12 15:57:46.195942 +0000 UTC  done

```

The definition for the most recent build can be displayed:

```
$ ernest service definition demo --build 72c1306c-c6ee-4d9b-4230-617d0e969ea9-abf663d6c173d4af98e3ff20bb7e8dde
---
name: demo
datacenter: my-dc
vpc_id: vpc-abcdef01

networks:
  - name: public
    subnet: 10.0.10.0/24
    public: true
  - name: private
    subnet: 10.0.11.0/24
    public: false
    nat_gateway: private-nat

nat_gateways:
  - name: private-nat
    public_network: public

security_groups:
  - name: public-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 52.211.19.211/32
        protocol: tcp
        from_port: '22'
        to_port: '22'
  - name: private-sg
    egress:
      - ip: 0.0.0.0/0
        protocol: any
        from_port: '0'
        to_port: '65535'
    ingress:
      - ip: 10.0.0.0/16
        protocol: tcp
        from_port: '22'
        to_port: '22'

instances:
  - name: public
    elastic_ip: true
    type: t2.micro
    image: ami-ed82e39e
    network: public
    start_ip: 10.0.10.11
    count: 1
    key_pair: my-key
    security_groups:
      - public-sg

  - name: private
    type: t2.micro
    image: ami-ed82e39e
    network: private
    start_ip: 10.0.11.11
    count: 1
    key_pair: my-key
    security_groups:
      - private-sg

```

## Clean-up

After we have finished with our service we can remove it:

```
$ ernest service destroy demo
Are you sure? Please type yes or no and then press enter: yes

Deleting nats:
 - my-dc-demo-private-nat
   Status    : completed
Nats deleted

Deleting instances:
 - my-dc-demo-public-1
   IP        : 10.0.10.11
   PUBLIC IP : 52.210.179.96
   AWS ID    : i-abcdef01abcdef011
   Status    : completed
 - my-dc-demo-private-1
   IP        : 10.0.11.11
   PUBLIC IP : 
   AWS ID    : i-abcdef01abcdef012
   Status    : completed
Instances deleted

Deleting networks:
 - my-dc-demo-public
   IP     : 10.0.10.0/24
   AWS ID : subnet-defabc01
   Status : completed
 - my-dc-demo-private
   IP     : 10.0.11.0/24
   AWS ID : subnet-defabc02
   Status : completed
Networks deleted

Deleting firewalls:
 - my-dc-demo-public-sg
   Status    : completed
 - my-dc-demo-private-sg
   Status    : completed
Firewalls deleted
SUCCESS: your environment has been successfully deleted

```

## Next Steps

Take a look at the [AWS YAML reference](/documentation/aws-yaml/).