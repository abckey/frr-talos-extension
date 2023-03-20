# Customized FRR talos system extension image for BGP routing on the Kubernetes host

***

FRR container is used for Routing on the Host, it also peers with Metallb BGP speaker, to receive route update of load balancer IPs from Metallb, then further send to Leaf routers. 

docker start script template [docker-start.j2](docker-start.j2) is substitued during docker image build, `/usr/lib/frr/docker-start` is then generated as container start script.

frr configuration template [frr.conf.j2](frr.conf.j2) is pre-built into container, variables in the template will be substituted during container start-up, with server local specific values, `/etc/frr/frr.conf` then gets generated out of it.

Following parameters are hardcoded, will be the same on each server:

- `ASN_METALLB_LOCAL`: `4200099998`
- `ASN_METALLB_REMOTE`: `4200099999`
- `NAMESPACE_METALLB`: `metallb`
- `PEER_IP_LOCAL`: `192.168.250.254`
- `PEER_IP_REMOTE`: `192.168.250.255`
- `PEER_IP_PREFIX`: `31`
- `INTERFACE_MTU`: `1500`

> Above hardcoded variables are placed into container image as `/etc/frr/env.sh`, to be sourced by start script.

To make the FRR system extenion to work, following dynamic varialbes need to be configured in talos machine config:

- `NODE_IP`: This is basically the /32 node-ip on `lo` or `dummy0` , defined as environment variable from talos machine config
- `ASN_LOCAL`: frr local AS Number for upstream peering, defined as environment variable from talos machine config

Example machine config snippet:

```
machine:
  env:                                                                                                                                                                                                               ASN_LOCAL: 4200001001
    NODE_IP: 10.10.10.10
```

Build the image locally

```
docker build -t frr-talos-extension .
```





