+++
date = "2016-10-24"
title = "Developer documentation - Integration tests"
category = "developer"
showChildren=true
[menu.developer]
  name = "Integration tests"
  weight = -93
  identifier = "integration-tests"
  parent = "Testing"
+++

# Integration tests

As ernest is a distributed system it's sometimes hard to have integration tests, this is why some key services have integration tests + per service tests.

These integration tests are building a partial ernest environment with docker-compose, but they're mocking all connectors with [all-all-fake-connector](https://github.com/ernestio/all-all-fake-connector) which basically responds what's suposed by the connectors.

