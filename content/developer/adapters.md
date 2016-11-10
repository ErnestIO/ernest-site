+++
date = "2016-10-24"
title = "Developer documentation - Component adapters"
category = "developer"
showChildren=true
[menu.developer]
  name = "Component adapters"
  weight = -92
  identifier = "adapters"
  parent = "Architecture"
+++

### Adapters

An adapter is getting an order for a single component creation and it decides which connector will process this creation. 

Some of them use a translator so the internal component structure gets adapted to the connector, however, same as we are doing with builders, we're trying to deliver as much adapters as possible on top of the generic-adapter.
