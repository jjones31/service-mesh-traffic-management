# Deploy Courie 
_Welcome  back to the Service Mesh Lab. In this section of the lab we are going to deploy Courie. Courie consists of two sub applications, Courie Driver and Courie Web. In the Courie Driver application drivers will receive notifications to pick up packages and deliver them to designated locations. In the Courie Web application. users select destinations for packages to be delivered and a driver comes to pick up the package and deliver it._   

>Steps for deploying Courie
>- Deploy Courie Delivery Service
>- Deploy Courie Driver Service
>- Deploy Courie Driver Web
>- Deploy Courie Web Application


Welcome to the Service Mesh Application Development labs. Today we are going to walk you through how to get your environment set up and ready to perform service mesh related tasks in future labs. In the first section of this lab will start by show you how to order your lab environment from OpenTLC labs.

## Provision OpenShift Service Mesh Lab in OpenTLC
Open [https://labs.opentlc.com/](https://labs.opentlc.com/) in your browser of choice and sign in with your OpenTLC username.   
_Your username is your Red Hat email with a dash in place of the @._   
<!-- ![login](login.png) -->
1. Sign in
2. Click *Services*
3. Click *Catalogs*
4. Click *OPENTLC OpenShift 4 Labs*
5. Click *OpenShift 4 Service Mesh Lab*
6. Click *Order*.   
![catalogs](catalogs.png)   
![smlab](smlab.png)

## Setup Projects
Clone the Service Mesh Traffic Management lab and CD into the `service-mesh-traffic-management` directory.
```
git clone https://github.com/jjones31/service-mesh-traffic-management


cd service-mesh-traffic-management
```   
Create an environmental variable for `LAB_HOME` and then go into the cluster-setup directory and apply the `0-projects.yaml` to create the projects on your cluster.
```
LAB_HOME=$(pwd)

cd $LAB_HOME/cluster-setup

oc apply -f 0-projects.yml
```

## Setup Operator Subscriptions
Install the operator subscriptions and wait for them to become available before moving on.   
```
oc apply -f 1-operator-install.yaml

kubectl wait subscription --all --timeout=-1s --for=condition=Available -n openshift-operators

kubectl wait subscription --all --timeout=-1s --for=condition=Available -n courie-pipeline

```

## Deploy the Service Mesh Control Plane and ServiceMeshMemberRoll
Applying the following file deploys grafana, pilot, kiali, mixer and istio gateways as well as creates a ServiceMeshMemberRoll in the `istio-system` namespace with `courie` as the only member.
```
oc apply -f 2-service-mesh.yaml
```

## Deploy AMQ Streams
Courie uses AMQ streams for event processing. Apply `3-amq-streams.yaml` to deploy an instance of AMQ Streams. For more info on [AMQ Streams](https://developers.redhat.com/products/amq/overview).
```
oc apply -f 3-amq-streams.yaml
```

## Deploy Redis
Courie uses `Redis` as its default cache service. Apply `4-redis.yaml` to deploy an instance of Redis.  For more info on [Redis](https://www.redis.io/).
```
oc apply -f 4-redis.yaml
```

## Deploy Jenkins 
Courie uses `Jenkins` for the CICD pipeline. Apply `5-courie-cicd.yaml` to deploy an instance of Jenkins. For more info on [Jenkins](https://www.jenkins.io/).
```
oc apply -f 5-courie-cicd.yaml
```

## Setup Over!
_Nice work! In the next lab we will deploy the Courie Delivery Service._