# Kube Kong Operator

Kube Kong Operator helps you load test your Kubernetes applications by simulating production-like load on your dev cluster.

## What is offered?

Kube Kong is a [Kubernetes Operator](https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-operator) designed to do the following :

* Creating specified number and kind of resources in a namespace

* Generating requests to k8s apiserver to simulate a production-like load

## Installation

### Installing on OpenShift 3.x (without OLM)

Login to your cluster and create a namespace for the operator :

```sh
oc new-project king-kong
```

Install the operator :

```sh
oc apply -f ./deploy/non-olm/operator.yml
```

Ensure that the operator pod is in `Running` state :

```sh
$ oc get pod -l name=kube-kong-operator 
NAME                                  READY   STATUS    RESTARTS   AGE
kube-kong-operator-5c8467bcd6-xp6rn   2/2     Running   0          26m
```

### Installing Kube Kong on multiple namespaces

See documentation to launch kube kong workloads on multiple namespaces [here](tools)

## Usage

Kube Kong creates two new Custom Resources on the cluster to help users interact with the operator :

1. __KongArmy__
2. __KongBlitz__

### KongArmy Custom Resource

KongArmy CR lets users configure the resources they want to create through the operator. 

_KongArmy.example.yml_

```yml
apiVersion: konveyor.openshift.io/v1alpha1
kind: KongArmy
metadata:
  name: example-kongarmy
  namespace: king-kong
spec:
  teardown: false

  requested_resources:
    - kind: Secret
      count: 2

    - count: 100
      definition: 
        kind: ExampleCustomResource
        apiVersion: example.openshift.io/v1alpha1	
        metadata:
          namespace: king-kong
        spec:
          data: kong-operator-is-awesome
```
#### KongArmy Configuration

`requested_resources` is an array of resources with each item containing the following keys :

| Variable     	| Description                                                                         	|
|--------------	|-------------------------------------------------------------------------------------	|
| `kind`       	| Kind of resource to create.                                                         	|
| `count`      	| Number of resources to create.                                                      	|
| `definition` 	| Definition of the resource to create Only Applicable when `kind` is not  specified. 	|

Please note that `kind` and `definition` are mutually exclusive fields. 

When `kind` is specified, the operator will use its own default definition for that resource. Currently, following `kind`s are supported :
1. Pod
2. Deployment
3. ConfigMap
4. Secret

When `kind` is not defined, you have to provide your own definition for the resource using `definition` field. This is particularly useful if you want to use other resources than the default supported ones.


`teardown` is a boolean flag to request operator to teardown all the resources it created. 

### KongBlitz Custom Resource

KongBlitz CR defines different actions the operator will perform on resources to simulate load.

_KongBlitz.example.yml_

```yml
apiVersion: konveyor.openshift.io/v1alpha1
kind: KongBlitz
metadata:
  name: example-kongblitz
  namespace: king-kong
spec:
  perform_actions: true

  resources:
    - kind: Secret
      api_version: v1
      namespace: king-kong
      time_delta: 2
      actions:
      - label
```

#### KongBlitz Configuration

`perform_actions` is a boolean flag to request the operator to start or stop performing actions.

`resources` is a list of resources where each item in the list contains following keys:

| Variable      	| Required? 	| Description                                                                                                                                                           	|
|---------------	|-----------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| `kind`        	| Yes       	| Kind of the resource                                                                                                                                                  	|
| `api_version` 	| No        	| `apiVersion` of the resource. Defaults to `v1`                                                                                                                        	|
| `namespace`   	| No        	| Namespace of the resource. Defauts to the namespace in which the operator is deployed.                                                                                	|
| `time_delta`  	| Yes       	| Time spacing between action generation in the multiples of reconcile period. Reconcile period is 5s. To generate actions every 10s, `time_delta` would be set to `2`. 	|
| `actions`     	| Yes       	| List of actions to perform. See supported actions below. 

___Supported Actions___

Currently supported actions include:
* label
* annotation
* delete
