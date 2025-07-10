# gofrm59
Repository for the presentation of the Gophers Meetup in Frankfurt on 10.07.2025


## Creating an Operator with kubebuilder

### Prerequisites
* go (version > 1.21)
* kubebuilder
* docker (At least I used Docker)
* kubernetes cluster (kind for example)

### Commands
#### Creation

Creating the operator/project
```sh
kubebuilder init --domain <domain-name> --repo <git-url>/<>/<operator-name> --skip-go-version-check
```

Creating the API
```sh
kubebuilder create api --group <group-name> --version v1 --kind <operator-name> --resource --controller
```

#### Testing and provisioning 

Deploying the API to Kubernetes
```sh
make install 
```

Running the operator on your own device
```sh
make run
```

#### Pushing operator to registry
Login to registry
```sh
docker login <registry-domain
```

```sh
sudo make docker-build docker-push IMG=<registry-url>/<operator-name>:0.1
```

#### Deploying operator to kubernetes
```sh
make deploy IMG=<registry-url>/<operator-name>:0.1
```

#### Removing operator
Removing Controller
```sh
make undeploy
```

Removing API
```sh
make uninstall
```


### Troubleshooting
I came across those two issue:
* [metadata.annotations: Too long: must have at most 262144 bytes](https://github.com/kubernetes-sigs/kubebuilder/issues/2556)
* [Panic Related to Garbage Collector When Running Go Program](https://stackoverflow.com/questions/71942328/panic-related-to-garbage-collector-when-running-go-program)


## Router commands
### CLI Commands on Arista cEOS connected via ssh
```sh
ssh admin@<ip>

ceos1>en
ceos1#conf t
ceos1(config)#hostname ceos01
ceos01(config)#ip routing
ceos01(config)#router ospf 1
ceos01(config-router-ospf)#network 10.0.0.0 0.0.0.255 area 0
ceos01(config-router-ospf)#interface Ethernet1
ceos01(config-if-Et1)#no switchport
ceos01(config-if-Et1)#ip address 10.0.0.1/24

# Exit and save
ceos01(config)# end
ceos01# write memory

```


### gnmi

```sh
# Not entirely complete (WIP)
gnmic -a <ip>:<port> -u admin -p admin --insecure set \
  --update-path "/interfaces/interface[name=Ethernet1]/subinterfaces/subinterface[index=0]/ipv4/addresses/address[ip=10.0.0.1]/config" \
  --update-value '{"ip": "10.0.0.1", "prefix-length": 24}' \
  --update-path "/interfaces/interface[name=Ethernet1]/config/enabled" \
  --update-value true \
  --update-path "/network-instances/network-instance[name=default]/protocols/protocol[identifier=OSPF][name=1]/config" \
  --update-value '{"identifier": "OSPF", "name": "1", "enabled": true}' \
  --update-path '/interfaces/interface[name=Ethernet1]/config/type' \
  --update-value 'iana-if-type:ethernetCsmacd' \
  --update-path "/network-instances/network-instance[name=default]/protocols/protocol[identifier=OSPF][name=1]/ospfv2/areas/area[identifier=0]/interfaces/interface[id=Ethernet1]/config/id" \
  --update-value "Ethernet1" \
  --update-path "/network-instances/network-instance[name=default]/protocols/protocol[identifier=OSPF][name=1]/ospfv2/areas/area[identifier=0]/config/identifier" \
  --update-value 0

```

Check with th‚e following command
```sh
gnmic -a <ip>:<port> -u admin -p admin --insecure get \
  --path '/interfaces/interface[name=Ethernet1]/subinterfaces/subinterface[index=0]/ipv4/addresses'

```

