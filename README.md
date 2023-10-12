# php-app-migration
Based on https://github.com/jharmison-redhat/php-app-migration but configured to use the pre-imported VMs in the "vmimported" namespace of the OpenShift Virtualization Roadshow.

## Setup
1. In the OpenShift Console, navigate to "Virtualization > VirtualMachines" and start VMs: database, winweb01, winweb02

1. Clone this git repository
   ```sh
   git clone https://github.com/mattzager/php-app-migration.git
   cd php-app-migration
   ```

1. In a terminal, ssh to the bastion host, then ssh to the host indicated for CLI access.

1. Ensure you are using the 'vmimported' project. (You should be automatically logged in as an admin when you use the oc commands.)
   ```sh
   oc project vmimported
   ```

1. Next we want to label existing VM pods with app tags, create services for both the database and web servers, and create an external route to access the web application.
   ```sh
   oc label pods -l vm.kubevirt.io/name=database app=database
   oc label pods -l vm.kubevirt.io/name=winweb01 app=winweb
   oc label pods -l vm.kubevirt.io/name=winweb02 app=winweb
   oc apply -k 00-vmimported-setup
   ```

4. After this step, let's ensure the services and routes are working as expected. In the OpenShift Console, navigate to "Networking > Routes" (make sure you have selected 'vmimported' in the "Project:" drop down) and click on the route location URL which should open a browser where you can see the PHP application output which reports your visitor number and which host served the web page.

## Migration
1. Create a new build to deploy the web application in containers, and update the route to include the containers to enable shutting down of the VMs.
   ```sh
   oc apply -k 01-vmimported-migration
   ```
   
## Walkthrough
1. TODO
