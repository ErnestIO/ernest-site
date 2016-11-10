+++
date = "2016-10-24"
title = "Developer documentation - Definition mappers"
category = "developer"
showChildren=true
[menu.developer]
  name = "Definition mappers"
  weight = -95
  identifier = "definition-mappers"
  parent = "Architecture"
+++

# Definition mappers

Definition mappers basically map your input yaml/json file on a valid internal struct at the time they do an initial validation.

#### definition-mapper

Definition mapper service basically decides which provider specific mapper will handle the input mapping.


#### vcloud-definition-mapper

Validates and maps the VCloud yaml input on a valid internal service.


#### aws-definition-mapper

Validates and maps the AWS yaml input on a valid internal service.
