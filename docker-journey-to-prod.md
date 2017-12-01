# Journey to Production - Decisions/Path

## Linux Distribution Matters

- Docker is kernel and storage driver dependent
- Miniumum version not best version
- Ubuntu 16.04.2 LTS
 - Popular, well-tested with Docker
 - One of latest LTS kernels
 - Wide storage driver support
- Use Docker versions from https://store.docker.com and not

## Architecture Evolution

### 1 node swarm

- good starting point

### 3 node swarm

- only one node can fail
- pet projects
- test/ci setups

### 5 node swarm

- all managers and workers
- two nodes can fail
- minimum for real prod

### 10 node swarm

- 5 dedicated managers (only 1 is active)
- some workers in DMZ
- anything beyond 5 nodes
- control container placement with labels + constraints

```
docker node update —label-add data-drive=ssd worker1
docker node update —label-add net-segment=ssd worker2
docker servie create \
 —constraint node.labels.data-drive==ssd mysql
```

### 100 node swarm

- 5 dedicated managers (only 1 is active)
- resize managers as you grow
- multiple managers as you grow
- multiple worker subnets on private/dmz
- control container placement with labels and constraints

### Example with 3000 nodes

https://github.com/swarmzilla/swarm3k

## multiple swarms?

### not-so good reasons

- different hardware configs
- different subnets & security groups
- solution: use node labels with container constraints

### good reasons

- geographical boundaries between nodes (e.g. different regions)
- security boundaries compliance (PCI)
- personal boundaries for swarm management (e.g. multiple ops teams, or delegation in managent -> alternative migrate to Docker EE UCP with auth plugin to manage user rights)

## outsource well-defined plumbing

- beware of the “not implemented here” syndrome (siymilar to “not invented here”) -> basically means do not aim to do everything on your own

- opportunities for outsourcing
 - where it can still be a challenge to implement and maintain
 - and where SaaS market is mature

- for your consideration
 - image registry
 - log aggragation, storage, search and alerting (e.g. 17.05 gets service logs)
 - monitoring and alerting

## no time at all?!

one container per vm

- least amount of infrastructure change while leeting you:
 - run dockerfile recipes rather than Puppet
 - improve docker manegemnt skills
  - simplify your VM OS build
- Moby Project & Docker LinuxKit will likely make this more popular
 - https://mobyproject.org
 - https://github.com/linuxkit/linuxkit
- Windows is doing similar with Hyper-V Containers
- Linux is doing similar with Intel Clear Containers

## other ways to make impact

- use containers for tech support (mock environemnts)
- temp test environments, customer demos
- wait for someone to deploy/config an vm

