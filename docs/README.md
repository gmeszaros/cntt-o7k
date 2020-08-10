# Openstack Reference Model

## Introduction
OpenStack is a cloud operating system that controls large pools of compute, storage, and networking resources throughout a datacenter, all managed and provisioned through APIs with common authentication mechanisms.

The Reference Model (RM) specifies a virtualisation technology agnostic (VM-based and container-based) cloud infrastructure abstraction and acts as a “catalogue” of the exposed infrastructure capabilities, resources, and interfaces required by the workloads.

This document specifies the design details for Charmed Openstack from Canonical:
1. **Design Overview**. The Design Overview provides a high-level presentation that describes the Openstack deployment strategy, architecture and overall design.
2. **Charm Bundle**. The Charm Bundle describes the Reference Implementation for Openstack. It is a set of YAML files that is used to build the same cloud at any time and is used as the base for Infrastructure as Code.

CNTT Cloud Infrastructure Architecture [Chapter 03](https://github.com/cntt-n/CNTT/blob/master/doc/ref_arch/openstack/chapters/chapter03.md) describes the Reference Architecture that includes the Network Function Virtualisation Infrastructure (NFVI) and Virtual Infrastructure Manager (VIM).

![https://github.com/cntt-n/CNTT/raw/master/doc/ref_arch/openstack/figures/RA1-Ch03-Core-Cloud-Infra-Services.png](https://github.com/cntt-n/CNTT/raw/master/doc/ref_arch/openstack/figures/RA1-Ch03-Core-Cloud-Infra-Services.png)

## Technologies

### Ubuntu Server
The VIM elements are deployed on bare metal machines running Ubuntu Server Operating System. Ubuntu Server is the OS produced by Canonical for server and cloud infrastructure. It is used as the base platfor for the Reference Implementation. Ubuntu is a Linux based operating system with commercial support and follows release cycle available at https://www.ubuntu.com/about/release-cycle.


### MAAS
MAAS, Metal as a Service is an open-source self-service provisioning tool from Canonical used to configure and deploy bare metal machines.


### Juju
Juju is an open source application modeling tool that allows you to deploy, configure, scale and operate cloud infrastructures quickly and efficiently.

Juju is used for life-cycle managenent and it uses a model description as an input to deliver an environment according to a set of requirements defined in the model.

Juju consists of a number of components:
- Controller nodes (physical or virtual) which hosts API and database components of Juju;
  - Controllers include a machine provider-specific provisioning logic dependant on an underlaying substrate.
  - Juju client which is used to bootstrap and manage Juju itself and models provisioned via Juju;
  - Machine agents are used to perform post-deployment machine-related tasks that are relevant for Juju operation and report to Juju controller nodes;
  - Unit agents execute application lifecycle events. The concrete application-specific logic is encoded in Charms.

A detailed Juju documentation can be found at https://jaas.ai/.

### Charms
Charms encode the operational knowledge about a particular application in a reusable way. The operational knowledge in question is about deployment, scaling, upgrades, post-deployment reconfiguration, setting up software in a highly available configuration, integrations with other applications etc.

Charms rely on Juju to provide them with a platform for distributed and event-driven code execution, data exchange and a data model for common operations. However, the specifics of how to use that platform for a particular application are outside of Juju and are implemented in concrete charms.

The generic approach to operations taken in Juju and Charms makes Charms an exceptional tool not only in deploying and operating simple applications like load balancers or web servers but also complex applications such as OpenStack or Kubernetes.

Most of the software deployment in this engagement will be done using Juju and Charms. All charms that will be used in this project are open source and available at https://jujucharms.com.

Bundles are available for a wide set of complex applications and software stacks, including Openstack and Kubernetes. You can find the Charmed Openstack bundle at [openstack-bundles](https://github.com/openstack-charmers/openstack-bundles).

### Release Schedule for Openstack Charms
The cadence and other requirements are defined in the OpenStack Charms [Release Policy](https://docs.openstack.org/charm-guide/rocky/release-policy.html).

The OpenStack Charms team produces a release every 3 months, with every other release aligned to the main OpenStack release.

The current Release schedule is available at the openstack community website, under the [charm guide section](https://docs.openstack.org/charm-guide/latest/release-schedule.html)
