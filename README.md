# Piggy Metrics (Kubernetes) - API Gateway

[![CircleCI](https://circleci.com/gh/afermon/PiggyMetrics-gateway-service.svg?style=svg)](https://circleci.com/gh/afermon/PiggyMetrics-gateway-service) [![codecov](https://codecov.io/gh/afermon/PiggyMetrics-gateway-service/branch/master/graph/badge.svg)](https://codecov.io/gh/afermon/PiggyMetrics-gateway-service) [![GitHub license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/afermon/PiggyMetrics-gateway-service/blob/master/LICENCE)

As you can see, there are three core services, which expose external API to client. In a real-world systems, this number can grow very quickly as well as whole system complexity. Actually, hundreds of services might be involved in rendering of one complex webpage.

In theory, a client could make requests to each of the microservices directly. But obviously, there are challenges and limitations with this option, like necessity to know all endpoints addresses, perform http request for each peace of information separately, merge the result on a client side. Another problem is non web-friendly protocols which might be used on the backend.

Usually a much better approach is to use API Gateway. It is a single entry point into the system, used to handle requests by routing them to the appropriate backend service or by invoking multiple backend services and [aggregating the results](http://techblog.netflix.com/2013/01/optimizing-netflix-api.html). Also, it can be used for authentication, insights, stress and canary testing, service migration, static response handling, active traffic management.

Netflix opensourced [such an edge service](http://techblog.netflix.com/2013/06/announcing-zuul-edge-service-in-cloud.html), and now with Spring Cloud we can enable it with one `@EnableZuulProxy` annotation. In this project, I use Zuul to store static content (ui application) and to route requests to appropriate microservices. Here's a simple prefix-based routing configuration for Notification service:

```yml
zuul:
  routes:
    notification-service:
        path: /notifications/**
        serviceId: notification-service
        stripPrefix: false

```

That means all requests starting with `/notifications` will be routed to Notification service. There is no hardcoded address, as you can see. Zuul uses [Service discovery](https://github.com/afermon/PiggyMetrics-Kubernetes/blob/master/README.md#service-discovery) mechanism to locate Notification service instances and also [Circuit Breaker and Load Balancer](https://github.com/afermon/PiggyMetrics-Kubernetes/blob/master/README.md#http-client-load-balancer-and-circuit-breaker), described below.

For more information please refer to the main repository [afermon/PiggyMetrics-Kubernetes](https://github.com/afermon/PiggyMetrics-Kubernetes)

## Refereces
* Forked from [sqshq/PiggyMetrics](https://github.com/sqshq/PiggyMetrics)