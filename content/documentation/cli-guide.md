+++
date = "2016-06-06"
title = "CLI Guide"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "CLI Guide"
  weight = -80
  identifier = "cli-guide"
  parent = "Introduction"
+++

# Using the CLI

## Connecting to Ernest

The first step is to specify the IP address of our Ernest Server:

```
$ ernest target https://<Ernest Server IP>
Target set

```

Now we should login to Ernest with the built-in admin account:

```
ernest login
Username: admin
Password: *******

```
*(The default admin password is 'w4rmR3d')*

and change the admin password:

```
$ ernest user change-password
You're about to change your password, please respond the questions below: 
Current password: <old admin password>
New password: <new admin password>
Confirm new password: <new admin password>
Your password has been changed

```

Now we can create a user, a group, and add the user to the group:

```
$ ernest user create <user> <password>
User <user> successfully created

$ ernest group create <group>
Group <group> successfully created, you can add users with 'ernest group add-user username <group>'

$ ernest group add-user <user> <group>
User <user> is now assigned to group <group>

```

Now we can login with our new user:

```
$ ernest logout
Bye.

$ ernest login 
Username: <user>
Password: <password>
Welcome back <user>

```

We can confirm our Ernest Server IP and user:

```
$ ernest info
Current target : https://<Ernest Server IP>
Current user : <user>

```

## Datacenters

Ernest uses the concept of a **datacenter** to specify an infrastructure service provider, the provider API endpoint, credentials to access the provider API, and information specific to certain provider types. An example of creating a datacenter in Ernest for Amazon Web Services is:

```
$ ernest datacenter create aws --region region --secret_access_key secret_access_key --access_key_id access_key_id <dc name>

```

## Services

### What is a Service?

With Ernest a **service** is a collection of networking, virtual machines, and configuration options that collectively define an environment, and also the datacenter where the environment should be built. A service is defined in YAML format, and you can see examples of this in the provider-specific documentation.

### How to Create/Modify Services

Once we have created a datacenter and defined our service in YAML the process of building the service is:

```
$ ernest service apply <yaml file>

```

Once the service is created we can modify it by changing the YAML and then re-applying it. Ernest will calculate the differences and apply only those changes necessary to transition the service from the current state to the requested state.

Finally you can also destroy services if needed:

```
$ ernest service destroy <service name>

```

### View Service State and History

Ernest stores the state of each service, as well as all historical states. You can list the available service with:

```
$ ernest service list

```

For a given service you can see the history of all builds for that service:

```
$ ernest service history <service name>

```

We can view the YAML for a specific build by:

```
$ ernest service definition <service name> --build <build id>

```

We can view provider-generated information related to our service:

```
$ ernest service info <service name>

```

## Next Steps

See the [reference guide](/documentation/cli-cmds/) for a complete list of the commands available for the CLI.

See the provider-specific documentation for examples of how to use Ernest:

* [Amazon Web Services](/documentation/aws-intro/)
* [vCloud Director](/documentation/vcloud-intro/)
