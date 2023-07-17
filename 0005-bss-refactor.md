# Architecture Proposal for Refactoring BSS

## Context

The Boot Script Service is the main microservice involved in providing compute nodes with necessary information to boot images.  It was one of the first CSM services written in go and hasn't fundamentally changed in the intervening time.  While it still provides the service it was designed for, the needs of the system have evolved and some of the design choices have led to a system that is difficult to manage and brittle to change.  Large sites would like to refactor the system to make it easier to operate and update.

## Problems

Operationally, the choice of etcd as the backing store has led to unexpected operational overhead on large systems as operational teams need to manage larger and larger etcd clusters.  Additionally, the tight coupling with the hmetcd library makes iterative change difficult without unpredictable side effects.  The inclusion of cloud-init in the bss microservice makes it hard to substitute other cloud-init implementations without accessing private APIs of BSS and SMD.  Finally, the existing design doesn't support a clear boundary between areas of functionality according to DDD principles which defeats the stated goal of modularity.

## Proposed Changes

In order to shed some technical debt and replace etcd we are proposing several changes.

### Externalize the concept of a Compute Node as a first class object with a DDD approach

The basic API for all Components doesn't differentiate between bootable and non-bootable items through a strict hierarchy derived from the RedFish data model.  BSS, and other services that work primarily with managing compute nodes need to know a lot about the underlying data  

### 

1. Create a new microservice to replace BSS based on a standard chassis
    - Each BSS instance should maintain an in-memory representation of all nodes within the system that it is responsible for and is refreshed periodically or on-demand; failure of a remote datastore should be a survivable event.
2. Externalize the concept of a bootable node within the inventory system with a cacheable API suitable for BSS
    - Node as a first class object
    - Node includes bootable mac and ip as object members
    - Node includes selectors for partition information and boot groups within partitions
3. Replace the hmetcd library with a more standard library (gokv?)
4. Define an API boundary between BSS, SMD, and Cloud-Init that supports a modular Cloud-Init approach

## Correctness Proof





