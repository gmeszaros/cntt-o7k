# CNTT Openstack Deployment Guide

## High level design description

This guide describes the deployment of a Charmed Openstack bundle on top of a MAAS managed physical infrastructure.

## FCE installation

The [Foundation Cloud Engine](https://launchpad.net/cpe-foundation) tool is used to drive the deployment. It should be installed on a control node with sufficient network access to drive the deployment.

Clone and install the Foundation Cloud Engine:
```
git clone -b stable/2.7 git+ssh://${USERNAME}@git.launchpad.net/cpe-foundation ~/foundation
```
Install FCE:
```
cd ~/foundation
sudo ./install
```

## Configure FCE

All `fce` command invocations should be ran from the `./deployment` subdirectory.

Clone this reposiory and go to `./deployment/`:
```
mkdir -p keys
ssh-keygen -q -N "" -f keys/maas_id_rsa
```
Deploy MAAS and PostgreSQL onto the infra nodes, and set up any necessary commissioning scripts for the deployment:
```
fce build --layer maas --steps maas:preflight,maas:postgres,maas:maas_install,maas:maas_admin_setup,maas:setup_maas_as_dns,maas:maas_admin_login,maas:configure_maas
```
Networking

The networks connected to the servers hosting openstack are described in infra.yaml.  This includes fabrics, vlans, spaces, subnets, reserved blocks of IPs, DNS servers, etc.
```
fce build --layer maas --steps maas:configure_networks
```
Pods

Pods are on demand virtual machines managed by MAAS. Once a machine is defined in MAAS as a pod source, MAAS determines how many VMs can be provided with the machine resources available and commissions them on demand.

You can then execute the pod creation script:
```
fce build --layer maas --steps maas:setup_pods
```

Enlistment

Enlist the nodes:

```
fce build --layer maas --steps maas:enlist_nodes
```

Hardware Buckets

Dump all nodes info grouped by hardware configuration into “buckets.yaml” file.

```
fce build --layer maas --steps maas:generate_buckets
```

Edit “bucketsconfig.yaml” to differentiate node types based on the design.
```
vim config/bucketsconfig.yaml
```

Configure nodes according to “bucketsconfig.yaml”:
```
fce build --layer maas --steps maas:configure_nodes
```
The script will read buckets.yaml and bucketsconfig.yaml and make API calls to MAAS to configure each node.

In order to insure the idempotency of this command, any existing storage or networking configuration will be cleared prior to applying new configuration.

The node configuration can then be verified in MAAS. If there are any issues, this step can be re-run.



## Bootstrap juju
All `fce` command invocations should be ran from the `./deployment` subdirectory.

Juju will first be bootstrapped with a single controller, and then 2 more controllers will be deployed to enable HA for Juju. If the layer is being rebuilt, use `--force` flag.

```
fce build --layer juju_maas_controller
```

Once this command completes, a juju controller named "maas-controller" will be created and should work in an HA configuration.

You can verify this by running:
```
juju list-controllers  --refresh
```

## Magpie Layer
Magpie is a charm that is used to test the networks that are available and validates:
 - The MTU
 - The speed
 - The DNS records

It can be used by running the following command:
```
fce build --layer magpie
```

Similarly as before, it might be necessary to add --force to the arguments list.

## Openstack deployment
Before deploying the cloud, make sure that the configuration of your openstack bundle is correct.

Once the bundle is configured, launch fce with:
```
fce build --layer openstack
```

OpenStack Post-Deployment Steps

```
TBD
```

## Validating OpenStack
Validation of the cloud will be launched automatically after deployment on openstack layer phase if specified in master.yaml, 

### Automated Tests
The automated tests can be triggered separately, just run the following command:
```
fce validate --layer openstack
```
It will run the following tests:
 - Tempest
 - Rally
 - Rados Bench
 - FIO against ceph
 - Juju Lint (validation of the deployed bundle)


Once all the steps finished, the OpenStack environment is validated and ready to be used.
