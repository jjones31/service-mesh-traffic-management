# Service Mesh Deployments with Courie
Red Hat OpenShift Service Mesh offers many different features for traffic management, security, and obervability without the need to alter you application.

Traffic management is an extremely powerful feature by enabling dynamic changes to how services communicate without having to deploy. 

This repository contains the code for the scenarios discussed in Kickstart Innovation 2020, Using Red Hat OpenShift Service Mesh to Manage Complex Deployments. We cover examples of

 - Canaries
 - Dark Releases
 - Circuit Breakers
 - Fault Injection

## Cluster Prep
In the cluster-setup directory, run each resource bundle in order. Please note, you will need to wait after "1-operator-install.yaml" is done installing before preceding. 

## Courie Installation
Courie has 4 different services to deploy. Each service has a helm chart located in courie-deployment. You can install each service by invoking:

#### Set env variables
```
export API_KEY=<<INSERT_GOOGLE_MAPS_API_KEY>>
export CLUSTER_APP_BASE=apps.cluster.example.com
```

#### Driver Service
```
helm install driver-service-v1 --set firstInstall=true --set directionsApiKey=$API_KEY --set baseOpenshiftAppHostname=$CLUSTER_APP_BASE courie-driver-service
```

#### Delivery Service
```
helm install delivery-service-v1 --set firstInstall=true --set baseOpenshiftAppHostname=$CLUSTER_APP_BASE courie-delivery-service
```

#### Courie Web App
```
helm install courie-web-v1 --set firstInstall=true --set directionsApiKey=$API_KEY --set baseOpenshiftAppHostname=$CLUSTER_APP_BASE courie-web
```

#### Courie Driver Web App
```
helm install courie-driver-web-v1 --set firstInstall=true --set directionsApiKey=$API_KEY --set baseOpenshiftAppHostname=$CLUSTER_APP_BASE courie-driver-web
```

##### Deploying a new app version
To deploy another version of an app, you must set version, versionName, and dockerTag. This will create a new workload for the app in which you can control with VirtualServices and DestinationRules. 

The following example deploys version 2 of the courie-web application.

```
helm install courie-web-v2 --set version=2.0.0 --set versionName=v2 --dockerTag=2.0.0 courie-web
```

Once the deployments are finished, you can find the routes to all of Courie's services with:

```
oc get routes -n istio-system
```

## Running Service Mesh Scenarios
The folder servicemesh-scenarios contains templates to modify how the traffic behaves between v1 and v2 of courie-web. You can apply each of the templates using oc process and oc apply. 

#### Courie Web Canary
Running this template will update the VirtualService to distribute traffic to v1 and v2 of courie-web 50/50. You can notice the difference by refreshing the courie-web. v2 has a red border around the app and v1 does not.  

Apply the scenario with the following: 
```
oc apply -f 1-courie-web-canary.yaml -p CLUSTER_APPS_BASE_URL=$CLUSTER_APP_BASE | oc apply -f -
```
