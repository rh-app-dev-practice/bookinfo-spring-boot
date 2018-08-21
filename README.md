Bookinfo Spring Boot Reference Application
==========================================

This application is modeled from the [Istio Bookinfo](https://istio.io/docs/examples/bookinfo/)
application, but is 100% Spring Boot and has been built to model a production-grade application.
Additionally, this application attempts to overcome some common hurdles and roadblocks that
developers often run into.

This initial version doesn't have a dependency on Istio, but instead uses Hystrix for circuit breaker
functionality. The goal is to move this application forward with Istio and remove the legacy Spring
Cloud functionality.

Architecture
------------

This application exhibits a stereotypical Spring Boot/Cloud microservice architecture for an application
that is deployed to Kubernetes. It also has S2I and CI/CD capabilities baked in to leverage the
OpenShift platform.

Microservices
-------------

### `bookinfo-details`

This microservice take an ISBN number, and return book details by calling an external Google API.

(TODO) In the future, this service would benefit from caching of detail records.

### (TODO) `bookinfo-products`

This microservice registers a product by using an ISBN and details from `bookinfo-details`.
It will generate a consistent `productId` UUID based on the ISBN. This localized
repository of books can be used to populate a catalog.

### `bookinfo-ratings`

This microservice allows a reviewer to assign a rating (1-10) for a product. The ratings data is stored
in a `rating` table in a `ratings` database.

### `bookinfo-reviews`

This microservice allows a reviewer to write a free-form text review for a product. It also makes
service calls to `bookinfo-ratings` to retrieve ratings information and also to add new ratings
as they are submitted with reviews. The reviews data is stored in a `review` table in a `reviews` database.
Additionally, the service calls to `bookinfo-ratings` go through a circuit breaker and have a
retry mechanism .

Features
--------

### Basics

* `pom.xml` inherits from RHOAR BOM (Bill of Materials) and attempts to follow RHOAR booster conventions
* Includes Red Hat Maven repositories
* Multi-module with loose coupling between projects; each is independent except for common config
* Prevalent use of [Lombok](https://projectlombok.org/) to minimize code for data objects
* Separate `default` and `openshift` Spring Boot profiles
* Separate `default` and `openshift` Maven profiles
* Use of Google Books API as an example 3rd party web service


### Database

* Use of Spring Data and Hibernate as opposed to use JDBC APIs
* Separate Maven profiles for H2 and PostgreSQL, plus H2 for tests
* Strong preference to UUID database keys which work much better in a distributed system
(i.e. microservices) as there will not be possible identifier collisions or synchronization issues.
* UUID values are stored in PostgreSQL in the native `uuid` type, which improves performance.
* `PostgresH2UUIDType` Custom Hibernate type to dynamically select UUID database type depending on if the
PostgreSQL JDBC driver is available or not. If not available, UUIDs are stored as `varchar` in H2
which greatly improves SQL compatibility between PostreSQL and H2 when working with UUID data. The
default UUID type for H2 is `binary` which creates compatibility issues with SQL.
* PostgreSQL compatibility mode enabled for H2 along with PostgreSQLDialect.
* Example of using `data.sql` to automatically insert values into an empty ephemeral database
* TODO: Disable `data.sql` for OpenShift profile
* TODO: Flyway database migrations
* TODO: Timestamps/auditing on database operations; will also assist in sorting to find
the "latest" data by using a timestamp rather than a sequence number
* TODO: Connection pooling configuration for PostgreSQL

### Spring Cloud

* Spring Cloud Config Kubernetes to gain access to secrets and configmaps through Kubernetes API
* Hystrix circuit breaker (with custom config)
* Hystrix Dashboard (for visualizing circuit breakers)
* Feign HTTP client with Hystrix integration and fallbacks
* `FeignClient` URL configurable through Spring Boot profiles
* `FeignClient` decoupled from target service code
* TODO: Replace Hystrix with Istio circuit breaker
* TODO: Feign Retries

### APIs

* TODO: (In Progress, PoC Completed) API Gateway
* TODO: (In Progress, PoC Completed) Swagger with SpringFox


### Frontend

* TODO: https://github.com/murphye/hybrid-react-angular-spring-boot-apps
* TODO: https://medium.com/@SumanthShankar/on-demand-login-with-keycloak-angular-4-5-ngrx-backend-api-bookmark-able-links-ecb065dc7993, https://github.com/mauriciovigolo/keycloak-angular, http://blog.keycloak.org/2018/02/keycloak-and-angular-cli.html
* TODO: https://github.com/patternfly/patternfly-ng

### Security

* TODO: (In Progress, PoC Completed) Single Sign-On (Keycloak) Integration

### OpenShift Template

* TODO: OpenShift

### OpenShift S2I

* TODO: Custom builder for multi-module Maven project https://blog.openshift.com/maven-multi-module-projects-and-openshift


### OpenShift + Jenkins CI/CD

* TODO: https://github.com/redhat-cop/container-pipelines/tree/master/multi-cluster-spring-boot
* TODO: https://github.com/redhat-cop/container-pipelines/tree/master/blue-green-spring
* TODO: https://github.com/redhat-cop/container-pipelines/tree/master/basic-spring-boot

### Automated Testing

* TODO: JUnit 5 tests
* TODO: https://github.com/redhat-cop/container-pipelines/tree/master/cucumber-selenium-grid


### Istio






