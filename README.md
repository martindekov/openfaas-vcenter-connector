# OpenFaaS vcenter-connector

vcenter-connector is an OpenFaaS [event-connector](https://docs.openfaas.com/reference/triggers/) built to consume events from vCenter and to trigger functions.

[![Build Status](https://travis-ci.org/openfaas-incubator/vcenter-connector.svg?branch=master)](https://travis-ci.org/openfaas-incubator/vcenter-connector)

## Overview

With this project your functions can subscribe to events generated by the changes (i.e. events) in your vCenter installation - for instance a VM being created, turned on or deleted. This allows you to extend vCenter's functionality by writing functions to execute each time an event is fired. An example may be tagging a VM with the date it was last turned on or applying a tag showing which user made a change to an object. 

## Status

This project uses the [OpenFaaS Connector SDK](https://github.com/openfaas-incubator/connector-sdk).

vCenter credentials are stored in Kubernetes secrets.

## Supported Events

The following event types (incl. their subtypes) are supported and can be used to trigger functions:

- Alarm Event, e.g. `AlarmCreatedEvent`
- Datastore Event, e.g. `DatastoreDestroyedEvent`
- Host Event, e.g. `HostDisconnectedEvent`
- Resource Pool Event, e.g. `ResourcePoolCreatedEvent`
- VM Event, e.g. `VmPoweredOnEvent`

For further details and naming see the [vSphere Web Services API](https://code.vmware.com/apis/358/vsphere#/doc/vim.event.Event.html) documentation.

## Credentials

### Credentials within Kubernetes

When using the connector in Kubernetes, you will need to create a secret for the connector in the `openfaas` namespace.

```sh
kubectl create secret generic vcenter-secrets \
  -n openfaas \
  --from-literal vcenter-username=user \
  --from-literal vcenter-password=pass
```

At runtime these secrets will be mounted at `/var/openfaas/secrets/`. See [/yaml/kubernetes/connector-dep.yml](/yaml/kubernetes/connector-dep.yml) for more.

### Using credentials outside of Kubernetes

You can pass credentials via arguments (not recommended).

```sh
./vcenter-connector \
  -vc-user="admin" \
  -vc-pass="test1234" \
```

Or use a file and pass the name:

```sh
export secret_mount_path="/tmp/secrets/`

./vcenter-connector \
  -vc-user="" \
  -vc-pass="" \
  -vc-user-secret-name=vcenter-username \
  -vc-password-secret-name=vcenter-password
```

The default path is `/var/openfaas/secrets/` which can be overridden by setting the `secret_mount_path` environment variable.

## Examples / community

* You can find a detailed example using vSphere tags for `VmPoweredOnEvent` [here](docs/example.md).

* Robert Guske of VMware write up a detailed [blog post](https://rguske.github.io/post/event-driven-interactions-with-vsphere-using-functions-as-a-service/) on how he automated the integration between several VMware products with OpenFaaS and this vcenter-connector.

### VEBA

VMware have released an appliance which packages [OpenFaaS](https://github.com/openfaas/faas) and the OpenFaaS vcenter-connector (this repository) and called it "VEBA". The appliance uses [Photon OS](https://vmware.github.io/photon/).

* [Audit VM configuration changes using the vCenter Event Broker by Dennis Zimmer](https://itnext.io/audit-vm-configuration-changes-using-the-vcenter-event-broker-45f9f5ba21f2)

* [Blog post on VEBA by Doug Taliaferro](https://doogleit.github.io/2019/11/automate-host-maintenance-with-the-vcenter-event-broker-appliance/#) 

* [VEBA on GitHub](https://github.com/vmware-samples/vcenter-event-broker-appliance)

## Contributing

Please propose changes in an Issue before submitting a PR and sign-off commits.

See the [contribution guide for OpenFaaS](https://github.com/openfaas/faas/blob/master/CONTRIBUTING.md) for more.

### License

MIT

### Acknowledgements

This project is hosted and maintained by [OpenFaaS Ltd](https://www.openfaas.com/)

It makes use of the following components:

* [govmomi](https://github.com/vmware/govmomi) project providing Golang bindings for vCenter by Doug MacEachern, VMware
* [vcsim](https://github.com/vmware/govmomi/blob/master/vcsim/README.md) simulator tool by Doug MacEachern, VMware

The contact for OpenFaaS Ltd is [Alex Ellis](https://github.com/alexellis)

The contact for VMware is [Michael Gasch (VMware)](https://github.com/embano1)
