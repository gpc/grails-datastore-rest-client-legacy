# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Grails 8 plugin (`io.github.gpc:grails-datastore-rest-client-legacy`) that provides a low-level REST/HTTP client for Grails applications. It is a forward port of the original `grails-datastore-rest-client` that was removed from Grails core. The main module is `grails-datastore-rest-client/`.

## Commands

```bash
./gradlew build            # Full build + tests
./gradlew test             # Run Spock tests
./gradlew check            # All checks (includes tests)
./gradlew publishToMavenLocal  # Publish to local Maven cache
```

Run a single test class:
```bash
./gradlew :grails-datastore-rest-client:test --tests "grails.plugins.rest.client.RestBuilderSpec"
```

Key versions (in `gradle.properties`): Java 21, Grails 8.0.0-M6, project version `8.0.0-SNAPSHOT`. The build requires Gradle 9.6.0 (wrapper) and a JDK 21 toolchain — see `.sdkmanrc`.

## Architecture

The library wraps Spring's `RestTemplate` with a Groovy-friendly DSL. Entry points:

- **`RestBuilder`** — synchronous REST client; main public API. Supports GET/POST/PUT/DELETE with a `RequestCustomizer` DSL closure for headers, auth, content type, etc.
- **`AsyncRestBuilder`** — async wrapper over `RestBuilder` using Grails `@DelegateAsync`.
- **`RestResponse`** — wraps Spring's `ResponseEntity`; provides `.json` (parsed JSON), `.xml` (GPathResult), and `.text` accessors.
- **`RequestCustomizer`** — DSL class passed as the closure delegate when configuring a request (headers, basic auth, Accept/Content-Type, body).

Templated resource clients (`AbstractResourcesClient`, `JsonResourcesClient`, `XmlResourcesClient`, and their `Async*` variants) provide CRUD patterns on top of `RestBuilder` for RESTful collection resources.

**Message converters** (in `org.grails.datastore.gorm.rest.client.{json,xml,utils}`) handle serialization/deserialization: `JsonHttpMessageConverter`, `GsonHttpMessageConverter`, `GPathXmlHttpMessageConverter`, `WritableHttpMessageConverter`, `GrailsConverterHttpMessageConverter`, `NullSafeStringHttpMessageConverter`.

Tests use Spock (JUnit Platform runner). The three spec files cover `RestBuilder`, `AsyncRestBuilder`, and `JsonResourcesClient`.