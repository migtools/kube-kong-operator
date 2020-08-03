# Kong Operator Demo

## KongArmy

KongArmy creates a set of specified resources.

```yml
apiVersion: konveyor.openshift.io/v1alpha1
kind: KongArmy
metadata:
  name: example-kongarmy
  namespace: kong-test
spec:
  teardown: false

  requested_resources:
    - kind: Secret
      count: 100

    - count: 100
      definition:
        kind: ConfigMap
        apiVersion: v1
```

Following demo shows creation of Secret and ConfigMap resources:

![kongarmy](https://user-images.githubusercontent.com/9839757/89182915-77b4b980-d564-11ea-81e2-f3ea0cfa36dc.gif)

## KongBlitz

KongBlitz performs specified actions on resources to simulate load on k8s apiserver.

```yml
apiVersion: konveyor.openshift.io/v1alpha1
kind: KongBlitz
metadata:
  name: example-kongblitz
  namespace: kong-test
spec:
  perform_actions: true

  kongarmy_resources:
    actions:
      - label
    time_delta: 2
```

Following demo shows label updates on Secret resources through KongBlitz:

![kongblitz](https://user-images.githubusercontent.com/9839757/89182943-813e2180-d564-11ea-8ecb-e076df8bb6f2.gif)
