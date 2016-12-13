+++
date = "2016-05-17"
cssid = "download"
type = "download"
description = "Ernest makes it easier to develop and deploy applications in the Cloud. Ernest supports full-stack application orchestration that allows you to drive software and infrastructure as a unified system."
+++

# Downloads

## Get Ernest

Below are instructions for installing the latest stable release of Ernest ([Release Notes](https://github.com/ernestio/ernest/releases)).

### Docker

Ideal for: production Ernest.

Requirements: [Docker](https://docs.docker.com/engine/installation/), [Docker Compose](https://docs.docker.com/compose/install/), OpenSSL.

1. Clone the repo containing the Vagrantfile: `git clone https://github.com/ernestio/ernest.git`

2. Change to the cloned directory: `cd ernest`

3. Run the setup script: `./setup`

4. Enter the hostname when prompted.

Once the box is up Ernest will be available on IP of the Docker host.

## Get Ernest CLI

The Ernest CLI is distributed as a binary package for all supported platforms and architectures. To install Ernest CLI, find the newest version of the [appropriate package](https://github.com/ErnestIO/ernest-cli/releases) for your system and download it.

After downloading Ernest CLI, unzip it and move the binary to a directory that is on the PATH. See [this page](http://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux) for instructions on setting the PATH on Linux and Mac. [This page](http://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) contains instructions for setting the PATH on Windows.

## Get Salt

The Salt image can be downloaded from [here](http://download.ernest.io/r3-salt-master.zip). For the current version of Ernest it is not possible to specify the vCloud catalog name that Ernest will get the Salt image from, you will need to place the image in a catalog named 'r3'.