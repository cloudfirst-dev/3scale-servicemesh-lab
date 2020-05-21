# 3Scale ServiceMesh Integration Lab
This lab will walk through how 3Scale can be integrated with ServiceMesh to provide metering for consumers of API services.

## Concepts Covered
1. 3Scale Installation
1. 3Scale Istio Adapter Installation
1. Setting up a tenant
1. Setting up an API for ServiceMesh Integration
1. Creating an application to consume the api
1. Demonstrate limits based on plans

## Prerequisites
* Namespace of 3scale-demo
* Namespace of 3scale-sm
* Namespace of 3scale-apps

## 3Scale Installation

### Prerequisite Steps
This will setup your environment variables for steps completed throughout the lab

You will need to know what wildcard domain to deploy to (ex. if when you create a route in OCP it gets created as route-name.apps.example.com your wildcard domain should be apps.example.com)

1. Set environment variable
    ```
    export WILDCARD_DOMAIN=apps.example.com
    export STORAGE_CLASS_NAME=replace-with-storage-class-name
    ```

### Operator Installation
These steps can be skipped if the 3Scale operator is already installed.

1. Navigate to the console with a user that has privs to install an operator
1. Go to the 3scale-demo project
1. Click Operators on the left and click OperatorHub
1. Search for 3scale and click Red Hat Integration - 3scale
1. Click the Install button
1. Select the 3scale-demo namespace
1. Click the Subscribe button

### API Manager Installation
These steps are to create the necessary resources to integrate with the ServiceMesh.

1. Run helm to deploy the API management
    ```
    oc project 3scale-demo
    helm install 3scale 3scale-demo --set wildcardDomain=$WILDCARD_DOMAIN,storageClassName=$STORAGE_CLASS_NAME
    ```
1.  It will take a few minutes for everything to become online

### ServiceMesh and Demo Apps
These steps will provision a ServiceMesh control plane and a sample app to use with 3scale

1. Get the default access token for 3scale integration
    ```
    export THREESCALE_ACCESS_TOKEN="$(oc get secret system-seed -n 3scale-demo -o=jsonpath={'.data.ADMIN_ACCESS_TOKEN'} | base64 -D)"
    ```
1. Deploy the Mesh and Demo App
    ```
    helm install 3scale-apps 3scale-apps --set accessToken=$THREESCALE_ACCESS_TOKEN
    ```