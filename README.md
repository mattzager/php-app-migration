# php-app-migration
Based on https://github.com/jharmison-redhat/php-app-migration but configured to use the pre-imported VMs in the vmimported namespace of the OpenShift Virtualization Roadshow.

## Setup
Label the existing VM pods with app tags, create services for both the database and web servers, and create an external route to access the web application.
```sh
oc label pods -l vm.kubevirt.io/name=database app=database
oc label pods -l vm.kubevirt.io/name=winweb01 app=winweb
oc label pods -l vm.kubevirt.io/name=winweb02 app=winweb
oc apply -k 00-vmimported-setup
```
## Migration
Create a new build to deploy the web application in containers, and update the route to include the containers to enable shutting down of the VMs.
```sh
oc apply -k 01-vmimported-migration
```
## Walkthrough
1. TODO
