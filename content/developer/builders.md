+++
date = "2016-10-24"
title = "Developer documentation - Component builders"
category = "developer"
showChildren=true
[menu.developer]
  name = "Builders"
  weight = -93
  identifier = "builders"
  parent = "Architecture"
+++

# Component builders

A builder is basically a component scheduler. It receives a list of components and processes each component sequentially or in parallel.

## Sequential processing

In order to process the component sequentially you should set the field "sequential_processing" to true .

## Dependencies

In order to track the state of all components in a collection, state is stored on redis. When all components have completed, a return event is sent.
