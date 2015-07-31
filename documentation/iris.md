---
layout: page-with-sidenav
title: Iris
permalink: /documentation/iris/
breadcrumb: Iris
---

# Iris: The Illumineto Message Broker
Iris provides message brokering services throughout the Illumineto stack. Iris is based on RabbitMQ and Node.js

## Operating Mode
Iris operates primary as a topic exchange; which allows both direct and fanout type behavior.

## Exchanges
Iris provides various exchanges, segmented by the various layers of the Illumineto stack. Messages should target the appropriate exchange.

### Full-Stack `sys.0`
Defaults to full-stack broadcast

 1. `sys.1` Sources & Content Discovery
 2. `sys.2` Accounts & Authorizations
 3. `sys.3` Client & Stack Tracking Events
 4. `sys.4` Content Delivery Tracking Events
 5. `sys.5` Exceptions & Error Conditions

#### Queues

##### `sys.1.sources`

### Client `client.0`

### Debug `debug.0`
Defaults to developer settings `debug` or `debug.0`

 1. `debug.1` Production debug exchange
 2. `debug.2` Verbose debugging exchange

### Logs `log.0`
Standard application logs for internal consumers

 1. `log.1` Log events for external consumers
