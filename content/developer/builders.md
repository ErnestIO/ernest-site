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

A builder is basically a component list processor. It receives a list of components and process it sequentially or in parallel.

## Sequential processing

In order to process the component sequentially you should set as true the field "sequential_processing".

## Dependencies

In order to control that all components have been successfully processed it has a dependency on redis to store its status.

## Types

We can find different builders on current version of ernest. Even it's naming convention says its name should be *<component>-builder* many components are using a *generic-builder* which is able to manage different components.

