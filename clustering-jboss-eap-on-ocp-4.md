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

2.  Create an imagestream

    ```console
    $ oc create -f https://raw.githubusercontent.com/jboss-container-images/jboss-eap-openshift-templates/eap74/eap74-openjdk11-image-stream.json
    ```

3.  Create a new project

   ```console
   $ oc new-project eap-logging-demo
   Now using project "eap-logging-demo" on server ...
   ``` 

5.  d

    ```
    $ oc new-app --template=eap74-basic-s2i \
     -p IMAGE_STREAM_NAMESPACE=eap-logging-demo \
     -p EAP_IMAGE_NAME=jboss-eap74-openjdk11-openshift:7.4.0 \
     -p EAP_RUNTIME_IMAGE_NAME=jboss-eap74-openjdk11-runtime-openshift:7.4.0 \
     -p SOURCE_REPOSITORY_URL=https://github.com/jboss-developer/jboss-eap-quickstarts \
     -p SOURCE_REPOSITORY_REF=7.4.x \
     -p GALLEON_PROVISION_LAYERS=jaxrs-server \
     -p CONTEXT_DIR=helloworld-html5
   ```

## References:

* [Red Hat JBoss Enterprise Application Platform 7.4: Getting Starting with JBoss EAP for OpenShift Container Platform: 8.7 Clustering](https://docs.redhat.com/en/documentation/red_hat_jboss_enterprise_application_platform/7.4/html-single/getting_started_with_jboss_eap_for_openshift_container_platform/index#configuring_a_jgroups_discovery_mechanism)
