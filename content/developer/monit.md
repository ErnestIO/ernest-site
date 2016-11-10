+++
date = "2016-10-24"
title = "Developer documentation - Monit"
category = "developer"
showChildren=true
[menu.developer]
  name = "Monit"
  weight = -97
  identifier = "monit"
  parent = "Architecture"
+++

# Monit

This service is sending real time information about service creation to the end user by keeping a stream open with the client.

It is catching all internal messages and decides which ones will fire a new message through the stream

It exposes data streams through SSE(Server Side Events).
