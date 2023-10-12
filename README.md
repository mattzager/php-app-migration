# php-app-migration
Based on <https://github.com/jharmison-redhat/php-app-migration> configured to use the pre-imported VMs in the 'vmimported' namespace of the OpenShift Virtualization Roadshow.

## Setup
In this setup section you will be starting 3 virtual machines on OpenShift Virtualization that run a database and 2 instances of a PHP web application running on IIS on Microsoft Windows servers. You will be creating services to expose them on the internal OpenShift SDN (the web application service will automatically load balance between the two web application instances) as well as a route which exposes the web application service to clients outside the OpenShift cluster.

1. In the OpenShift Console, navigate to "Virtualization > VirtualMachines" and start VMs: 'database', 'winweb01', 'winweb02'

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

4. After this step, let's ensure the services and routes are working as expected. In the OpenShift Console, navigate to "Networking > Routes" (select 'vmimported' in the "Project:" drop down) and click on the route location URL which should open a browser where you can see the PHP application output which reports your visitor number and which host served the web page.

## Migration
In this section you will be using OpenShift's Source to Image (S2I) tools to build a new container image from the original PHP application code, store that image in OpenShift's container registry, deploy 2 instances of the container native version of the application, create a service to expose and load balance between those 2 instances and add that service to the existing route to be able to replace the legacy VM services when they are shut down. The new container based web applications will continue to use the VM based database.

1. Create a new build to deploy the web application in containers, and update the route to include the new containers service.
   ```sh
   oc apply -k 01-vmimported-migration
   ```
   
1. After this step you can observe the following
1.1 In the OpenShift Console, navigate to "Builds > Builds" and see a build 'linweb-#'
1.1 When the build completes, navigate to "Workloads > Deployments" and see 'linweb'
1.1 Finally navigate to "Workloads > Pods" and see 2 pods named 'linweb-XXXXXXXXX-XXXXX' with "Status" 'Running'

1. You can verify the migration by shutting down the 'winweb01' and 'winweb02' VMs and refreshing your browser tab you used to view the application and see the visitor number increased as expected and the hostname has changed.

## Walkthrough
Review the walkthough in James' original **[README](https://github.com/jharmison-redhat/php-app-migration/blob/main/README.md#basic-walkthrough)** for ideas on how to show the elements being used in the migration as well as how to demonstrate the completed migration.
