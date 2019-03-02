# RFC

## Container interface

This document describes a common interface for dependency injection containers.

The goal set by `ContainerInterface` is to standardize how frameworks and libraries make use of a container to obtain objects and parameters (called entries in the rest of this document).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

The word implementor in this document is to be interpreted as someone implementing the `ContainerInterface` in a dependency injection-related library or framework. Users of dependency injections containers (DIC) are referred to as user.

### Specification

#### 1.1 Basics

- The `His\Container\ContainerInterface` exposes two methods : `get` and `has`.

- `get` takes one mandatory parameter: an entry type. It MUST be a `typename`. A call to get MUST return the type implementation, or throws an exception if the type is not known to the container. Two successive calls to get with the same type SHOULD return the same value. However, depending on the implementor design and/or user configuration, different values might be returned, so user SHOULD NOT rely on getting the same value on 2 successive calls. While `ContainerInterface` only defines one mandatory parameter in `get()`, implementations MAY accept additional optional parameters.

- `has` takes one unique parameter: an entry type. It MUST return true if an entry type is known to the container and false if it is not. `has($service)` returning true does not mean that `get($service)` will not throw an exception. It does however mean that `get($service)` will not throw a `NotFoundException`.

#### 1.2 Exceptions

Exceptions directly thrown by the container MUST implement the `His\Container\Exception\ContainerException`.

A call to the get method with a non-existing `service` SHOULD throw a `His\Container\Exception\NotFoundException`.

#### 1.3 Additional features

This section describes additional features that MAY be added to a container. Containers are not required to implement these features to respect the `ContainerInterface`.

##### 1.3.1 Delegate lookup feature

The goal of the delegate lookup feature is to allow several containers to share entries. Containers implementing this feature can perform dependency lookups in other containers.

Containers implementing this feature will offer a greater lever of interoperability with other containers. Implementation of this feature is therefore RECOMMENDED.

A container implementing this feature:

- MUST implement the ContainerInterface
- MUST provide a way to register a delegate container (using a constructor parameter, or a setter, or any possible way). The delegate container MUST implement the ContainerInterface.

When a container is configured to use a delegate container for dependencies:

- Calls to the `get` method should only return an entry if the entry is part of the container. If the entry is not part of the container, an exception should be thrown (as requested by the `ContainerInterface`).
- Calls to the `has` method should only return true if the entry is part of the container. If the entry is not part of the container, false should be returned.

Important! By default, the lookup SHOULD be performed on the delegate container only, not on the container itself.

It is however allowed for containers to provide exception cases for special entries, and a way to lookup into the same container (or another container) instead of the delegate container.
