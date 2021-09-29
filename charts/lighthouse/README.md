# Lighthouse Helm Chart

This helm chart allows you to run [lighthouse](https://github.com/sigp/lighthouse) as a beacon or validator node as ethereum's consensus layer within a kubernetes cluster.

# Examples

## Beacon node on the Prater testnet connected to Goerli via Infura

```yaml
mode: "beacon"

extraArgs:
  - --network=prater
  - --eth1-endpoints=https://goerli.infura.io/v3/<YOUR_API_SECRET>
```

## Beacon nodes exposing the P2P service via NodePort

This will make your nodes accessible via the Internet using services of type [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport). It will allocate a service definition and a pre-defined node port for each replica. The allocation starts at `p2pNodePort.startAt`. When using `p2pNodePort.enabled` the exposed IP address on your ENR record will be the "External IP" of the node where the pod is running.

```yaml
replicas: 5

mode: "beacon"

p2pNodePort:
  enabled: true
  startAt: 40000
  portsOverwrite:
    "3": 32000
```

This would create 5 beacon nodes, exposed via Node Port services with the following configuration:
- Node 0: `40000`
- Node 1: `40001`
- Node 2: `40002`
- Node 3: `32000`
- Node 4: `40004`


## Validator node targeting a beacon node service

This example runs a validator on the prater network that targets a pre-existing `lighthouse-beacon` service:

```yaml
replicas: 1

mode: validator

extraArgs:
  - --network=prater
  - --enable-doppelganger-protection
  - --beacon-nodes=http://lighthouse-beacon:5052
```