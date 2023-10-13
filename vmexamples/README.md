# php-app-migration
Based on <https://github.com/jharmison-redhat/php-app-migration> configured to use the imported VMs in the 'vmexamples' namespace of the OpenShift Virtualization Roadshow.

## Setup
The setup for this demo is done as part of completing the "Migrating Virtual Machines" module of the OpenShift Virtualization Roadshow.

## Migration
In this section you will be using OpenShift's Source to Image (S2I) tools to build a new container image from the original PHP application code, store that image in OpenShift's container registry, deploy 2 instances of the container native version of the application, create a service to expose and load balance between those 2 instances and add that service to the existing route to be able to replace the legacy VM services when they are shut down. The new container based web applications will continue to use the VM based database.

1. In a terminal, ssh to the bastion host, then ssh to the host indicated for CLI access in your RHDP environment.

1. Clone this git repository
   ```sh
   git clone https://github.com/mattzager/php-app-migration.git
   cd php-app-migration/vmexamples
   ```

1. Ensure you are using the 'vmexamples' project. (You should be automatically logged in as an admin when you use the oc commands.)
   ```sh
   oc project vmexamples
   ```

1. Create a new build to deploy the web application in containers, and update the route to include the new containers service.
   ```sh
   oc apply -k 01-vmexamples-migration
   ```
   
1. After this step you can observe the following
   1. In the OpenShift Console, navigate to "Builds > Builds" and see a build 'linweb-#'
   1. When the build completes, navigate to "Workloads > Deployments" and see 'linweb'
   1. Finally navigate to "Workloads > Pods" and see 2 pods named 'linweb-XXXXXXXXX-XXXXX' with "Status" 'Running'

1. You can verify the migration by shutting down the 'winweb01' and 'winweb02' VMs and refreshing your browser tab you used to view the application and see the visitor number increased as expected and the hostname has changed.

## Walkthrough
Review the walkthough in James' original **[README](https://github.com/jharmison-redhat/php-app-migration/blob/main/README.md#basic-walkthrough)** for ideas on how to show the elements being used in the migration as well as how to demonstrate the completed migration.
