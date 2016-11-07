+++
date = "2016-06-06"
title = "CLI Command Reference"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "CLI Command Reference"
  weight = -70
  identifier = "cli-cmds"
  parent = "Introduction"
+++


# CLI Command Reference

`ernest target`
```
NAME:
   ernest target - Configure Ernest target instance.

USAGE:
   ernest target <ernest_url>

DESCRIPTION:
   Sets up ernest instance target.

   Example:
    $ ernest target https://myernest.com
```

`ernest info`
```
NAME:
   ernest info - Display system-wide information.

USAGE:
   ernest info  

DESCRIPTION:
   Displays ernest instance information.

   Example:
    $ ernest info
```

`ernest login`
```
NAME:
   ernest login - Login with your Ernest credentials.

USAGE:
   ernest login [command options]  

DESCRIPTION:
   Logs an user into Ernest instance.

   Example:
    $ ernest login

   It can also be used without asking the username and password.

   Example:
    $ ernest login --user <user> --password <password>
  

OPTIONS:
   --user value      User credentials
   --password value  Password credentials
```

`ernest logout`
```
NAME:
   ernest logout - Clear local authentication credentials.

USAGE:
   ernest logout  

DESCRIPTION:
   Logs out an user from Ernest instance.

   Example:
    $ ernest logout
```

`ernest user list`
```
NAME:
   ernest user list - List available users.

USAGE:
   ernest user list  

DESCRIPTION:
   List available users.

   Example:
    $ ernest user list
```

`ernest user create`
```
NAME:
   ernest user create - Create a new user.

USAGE:
   ernest user create [command options] <username> <password>

DESCRIPTION:
   Create a new user on the targeted instance of Ernest.

   Example:
    $ ernest user create <username> <password>

   You can also add an email to the user with the flag --email

   Example:
    $ ernest user create --email username@example.com <username> <password>
  

OPTIONS:
   --email value  Email for the user
```

`ernest user change-password`
```
NAME:
   ernest user change-password - Change password of available users.

USAGE:
   ernest user change-password [command options] [arguments...]

DESCRIPTION:
   Change password of available users.

   Example:
    $ ernest user change-password

    or changing a change-password by being admin:

    $ ernest user change-password --user <username> --current-password <current-password> --password <new-password>
  

OPTIONS:
   --user value              The username of the user to change password
   --password value          The new user password
   --current-password value  The current user password
```

`ernest user disable`
```
NAME:
   ernest user disable - Disable available users.

USAGE:
   ernest user disable <username>

DESCRIPTION:
   Disable available users.

  Example:
   $ ernest user disable <user-name>
```

`ernest group delete`
```
NAME:
   ernest group delete - Deletes a group.

USAGE:
   ernest group delete  

DESCRIPTION:
   Deletes a group by name

    Example:
      $ ernest group delete <name>
```

`ernest group list`
```
NAME:
   ernest group list - List available groups.

USAGE:
   ernest group list  

DESCRIPTION:
   List available groups.

   Example:
    $ ernest group list
```

`ernest group create`
```
NAME:
   ernest group create - Create a group.

USAGE:
   ernest group create  

DESCRIPTION:
   Create a group.

   Example:
    $ ernest group create <name>
```

`ernest group add-user`
```
NAME:
   ernest group add-user - Adds a user to a group.

USAGE:
   ernest group add-user  

DESCRIPTION:
   Adds a user to a group.

    Example:
      $ ernest group add-user <user-name> <group-name>
```

`ernest group remove-user`
```
NAME:
   ernest group remove-user - Removes an user from a group.

USAGE:
   ernest group remove-user  

DESCRIPTION:
   Removes an user from a group.

    Example:
      $ ernest group remove-user <username> <group name>
```

`ernest group add-datacenter`
```
NAME:
   ernest group add-datacenter - Adds a datacenter to a group.

USAGE:
   ernest group add-datacenter  

DESCRIPTION:
   Adds a datacenter to a group.

    Example:
      $ ernest group add-datacenter <datacenter-name> <group-name>
```

`ernest group remove-datacenter`
```
NAME:
   ernest group remove-datacenter - Removes a datacenter from a group.

USAGE:
   ernest group remove-datacenter  

DESCRIPTION:
   Removes an datacenter from a group.

    Example:
      $ ernest group remove-datacenter <datacenter name> <group name>
```

`ernest datacenter list`
```
NAME:
   ernest datacenter list - List available datacenters.

USAGE:
   ernest datacenter list  

DESCRIPTION:
   List available datacenters.

   Example:
    $ ernest datacenter list
```

`ernest datacenter create vcloud`
```
NAME:
   ernest datacenter create vcloud - Create a new vcloud datacenter.

USAGE:
   ernest datacenter create vcloud [command options] <datacenter-name>

DESCRIPTION:
   Create a new vcloud datacenter on the targeted instance of Ernest.

   Example:
    $ ernest datacenter create vcloud --user username --password xxxx --org MY-ORG-NAME --vse-url http://vse.url --vcloud-url https://myernest.com --public-network MY-PUBLIC-NETWORK mydatacenter

   Template example:
    $ ernest datacenter create vcloud --template mydatacenter.yml mydatacenter
    Where mydatacenter.yaml will look like:
      ---
      fake: true
      org: org
      password: pwd
      public-network: MY-NETWORK
      user: bla
      vcloud-url: "http://ss.com"
      vse-url: "http://ss.com"

  

OPTIONS:
   --user value            Your VCloud valid user name
   --password value        Your VCloud valid password
   --org value             Your vCloud Organization
   --vse-url value         VSE URL
   --vcloud-url value      VCloud URL
   --public-network value  Public Network
   --template value        Datacenter template
   --fake                  Fake datacenter
```

`ernest datacenter create aws`
```
NAME:
   ernest datacenter create aws - Create a new aws datacenter.

USAGE:
   ernest datacenter create aws [command options] <datacenter-name>

DESCRIPTION:
   Create a new AWS datacenter on the targeted instance of Ernest.

  Example:
   $ ernest datacenter create aws --region region --token token --secret secret my_datacenter

   Template example:
    $ ernest datacenter create aws --template mydatacenter.yml mydatacenter
    Where mydatacenter.yaml will look like:
      ---
      fake: true
      token: token
      secret: secret
      region: region
   

OPTIONS:
   --region value    Datacenter region
   --token value     AWS Token
   --secret value    AWS Secret
   --template value  Datacenter template
   --fake            Fake datacenter
```

`ernest datacenter update vcloud`
```
NAME:
   ernest datacenter update vcloud - Updates the specified VCloud datacenter.

USAGE:
   ernest datacenter update vcloud [command options] <datacenter-name>

DESCRIPTION:
   Updates the specified VCloud datacenter.

   Example:
    $ ernest datacenter update vcloud --user <me> --org <org> --password <secret> my_datacenter
  

OPTIONS:
   --user value      Your VCloud valid user name
   --password value  Your VCloud valid password
   --org value       Your vCloud Organization
```

`ernest datacenter update aws`
```
NAME:
   ernest datacenter update aws - Updates the specified AWS datacenter.

USAGE:
   ernest datacenter update aws [command options] <datacenter-name>

DESCRIPTION:
   Updates the specified AWS datacenter.

   Example:
    $ ernest datacenter update aws --token <my_token> --secret <mysecret> my_datacenter
  

OPTIONS:
   --token value   Your AWS valid token
   --secret value  Your AWS valid secret
```

`ernest datacenter delete`
```
NAME:
   ernest datacenter delete - Deletes the specified datacenter.

USAGE:
   ernest datacenter delete <datacenter-name>

DESCRIPTION:
   Deletes the name specified datacenter.

   Example:
    $ ernest datacenter delete my_datacenter
```

`ernest service list`
```
NAME:
   ernest service list - List available services.

USAGE:
   ernest service list  

DESCRIPTION:
   List available services and shows its most relevant information.

   Example:
    $ ernest service list
```

`ernest service apply`
```
NAME:
   ernest service apply - Builds or changes infrastructure.

USAGE:
   ernest service apply <file.yml>

DESCRIPTION:
   Sends a service YAML description file to Ernest to be executed.
   You must be logged in to execute this command.

   If the file is not provided, ernest.yml will be used by default.

   Example:
    $ ernest apply myservice.yml
```

`ernest service destroy`
```
NAME:
   ernest service destroy - Destroy a service.

USAGE:
   ernest service destroy [command options] <service_name>

DESCRIPTION:
   Destroys a service by its name.

   Example:
    $ ernest destroy myservice
  

OPTIONS:
   --force, -f  Force destroy command without asking for permission.
```

`ernest service history`
```
NAME:
   ernest service history - Shows the history of a service, a list of builds

USAGE:
   ernest service history <service_name>

DESCRIPTION:
   Shows the history of a service, a list of builds and its status and basic information.

   Example:
    $ ernest history myservice
```

`ernest service reset`
```
NAME:
   ernest service reset - Reset an in progress service.

USAGE:
   ernest service reset <service_name>

DESCRIPTION:
   Reseting a service creation may cause problems, please make sure you know what are you doing.

   Example:
    $ ernest reset myservice
```

`ernest service definition`
```
NAME:
   ernest service definition - Show the current definition of a service by its name

USAGE:
   ernest service definition [command options] <service_name>

DESCRIPTION:
   Show the current definition of a service by its name getting the definition about the build.

   Example:
    $ ernest service definition myservice
  

OPTIONS:
   --build value  Build ID
```

`ernest service info`
```
NAME:
   ernest service info - $ ernest service info <my_service> --build <specific build>

USAGE:
   ernest service info [command options] <service_name>

DESCRIPTION:
   Will show detailed information of the last build of a specified service.
  In case you specify --build option you will be able to output the detailed information of specific build of a service.

   Examples:
    $ ernest service definition myservice
    $ ernest service definition myservice --build build1
  

OPTIONS:
   --build value  Build ID
```

`ernest service monitor`
```
NAME:
   ernest service monitor - Monitor a service.

USAGE:
   ernest service monitor <service_name>

DESCRIPTION:
   Monitors a service while it is being built by its service name.

   Example:
    $ ernest monitor my_service
```

`ernest preferences logger list`
```
NAME:
   ernest preferences logger list - Lists active loggers.

USAGE:
   ernest preferences logger list  

DESCRIPTION:
   List active loggers.

   Example:
    $ ernest preferences logger list
```

`ernest preferences logger add`
```
NAME:
   ernest preferences logger add - Creates / updates a logger based on its type.

USAGE:
   ernest preferences logger add [command options]  

DESCRIPTION:
   Creates / updates a logger based on its types.

   Example:
    $ ernest preferences logger add basic --logfile /tmp/ernest.log
    $ ernest preferences logger add logstash --hostname 10.50.1.1 --port 5000 --timeout 50000
    $ ernest preferences logger add rollbar --token MY_ROLLBAR_TOKEN
  

OPTIONS:
   --logfile value   Specify the path for the loging file
   --token value     Rollbar token
   --env value       Rollbar environment
   --hostname value  Logstash hostname
   --port value      Logstash port (default: 0)
   --timeout value   Logstash timeout (default: 0)
```

`ernest preferences logger delete`
```
NAME:
   ernest preferences logger delete - Deletes a logger based on its type.

USAGE:
   ernest preferences logger delete  

DESCRIPTION:
   Deletes a logger based on its types.

   Example:
    $ ernest preferences logger delete basic
```