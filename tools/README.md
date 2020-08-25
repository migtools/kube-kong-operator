# Kube Kong Operator workloads on multiple namespaces

The kube kong ansible launcher allows you to spawn multiple instances of the kube kong operator on any number of namespaces along with predefined resources

## Kube Kong launcher configuration file overview
The [Kube Kong launcher configuration](kong_config_sample.yaml) file contains all namespaces where kube kong operator will be deployed along with the resources that will be created in the kongarmy CR for each corresponding namespace. Please see the sample config below : 

```yaml
---
kong_config:
  - namespaces:
    - kong-army-1
    - kong-army-2
    - kong-army-3
    kong_army:
      teardown: false
      requested_resources:
      - count: 150
        kind: Secret
      - count: 100
        kind: ConfigMap
  - namespaces:
    - kong-army-4
    kong_army:
      teardown: false
      requested_resources:
      - count: 30
        kind: Pod
```

## Kube Kong launcher operating modes

The kube kong ansible launcher has two operation modes create(default) and destroy. See details below :

Create mode : 

* Create namespaces for each of the instances listed in the ansible launcher configuration file
* Deploy the kube kong operator in all namespaces requested on each instance
* Create a kubearmy CR with the requested resources in all namespaces listed on each instance

Destroy mode : 

* Remove all namespaces listed in the configuration file

## Usage

### Deploy Kube Kong workloads

1. Copy the sample kong_config_sample.yaml to kong_config.yaml (modify to your site needs)

```sh
cp kong_config.yaml.sample kong_config.yaml
```

2. Run the kube kong launcher playbook : 

```sh
ansible-playbook kong_launch.yaml
```

### Destroy Kube Kong workloads

```sh
ansible-playbook kong_launch.yaml -e teardown=true
```
