# Clustering Red Hat JBoss Enterprise Application Platform (EAP) 7.4 on OpenShift 4.x

## Overview

This document will deploy a sample distributable application in a Red Hat JBoss EAP 7.4 container on OpenShift 4.x, and step through the process needed to configure the EAP instances to correctly cluster on OpenShift. This is to support the following key concepts:

### High Availability (HA)

Improves service availability in the event of server failure.

### Scalability

A service can handle a large number of requests by spreading the workload across multiple servers.

### Failover

If a service fails, then the client can continue processing its tasks as another cluster member takes over the client's requests.

## Prerequisites

* A Red Hat OpenShift cluster at 4.14+
* The OpenShift `oc` client utility installed locally
* User account has sufficient credentials to create a new-project and manage resources within the project.

## Steps

1.  Login via the CLI

    ```console
    $ oc login ...
    ```

2.  Create a new project

    ```console
    #  oc new-project eap-cluster-demo
    Now using project "eap-cluster-demo" on server ...
    ``` 

3.  Create an imagestream

    ```console
    $ oc create -f https://raw.githubusercontent.com/jboss-container-images/jboss-eap-openshift-templates/eap74/eap74-openjdk11-image-stream.json
    ```

4.  Using the `oc new-app` utility, we'll bootstrap a project to build our sample application and deploy it

    ```console
    $ oc new-app --template=eap74-basic-s2i \
     -p IMAGE_STREAM_NAMESPACE=eap-cluster-demo \
     -p EAP_IMAGE_NAME=jboss-eap74-openjdk11-openshift:7.4.0 \
     -p EAP_RUNTIME_IMAGE_NAME=jboss-eap74-openjdk11-runtime-openshift:7.4.0 \
     -p SOURCE_REPOSITORY_URL=https://gitlab.com/ocp-demo/verify-cluster \
     -p CONTEXT_DIR=/ \
     -p SOURCE_REPOSITORY_REF=44474b4d4c2994cfc96eab9a547457063c3e3d39
    ```

5.  Get the route for your app and confirm it is reacble via your browser

    ```console
   echo http://$( oc get route eap-app -o jsonpath='{.spec.host}')/verify-cluster

    ```

7.  Open that url in your web browser, and you should see something like the following:

    ![Verify Site](/images/verify-cluster.png)

9.  

## Cleanup

1.  Delete the project

    ```console
    $ oc delete project eap-cluster-demo
    ```

## References:

* [Red Hat JBoss Enterprise Application Platform 7.4: Getting Starting with JBoss EAP for OpenShift Container Platform: 8.7 Clustering](https://docs.redhat.com/en/documentation/red_hat_jboss_enterprise_application_platform/7.4/html-single/getting_started_with_jboss_eap_for_openshift_container_platform/index#configuring_a_jgroups_discovery_mechanism)
* [Red Hat JBoss Enterprise Application Platform 7.4: Getting Starting with JBoss EAP for OpenShift Container Platform: Import the Latest JBoss EAP for OpenShift Imagestreams and Templates](https://docs.redhat.com/en/documentation/red_hat_jboss_enterprise_application_platform/7.4/html-single/getting_started_with_jboss_eap_for_openshift_container_platform/index#import_imagestreams_templates)
