# The FROG (v.4)

The FROG is a SDN/NFV/cloud service orchestrator that supports multiple heterogeneous domains.

The FROG is made of several components.
Given that a complex infrastructure is composed by several domains (e.g., SDN/OpenFlow network, OpenStack cloud), each (homogeneous) infrastructure domain is handled by a dedicated FROG domain orchestrator.
**Domain orchestrators**, each one responsible of a single infrastructure domain, cooperate by timely exporting (i.e., _publishing_) the capabilities and the available resources to a **message bus**. 
Finally, an **overarching orchestrator**, sitting on the same bus, will receive that information and will coordinate the deployment of the requested service across the entire infrastructure.

Supported domains include **SDN/OpenFlow** network infrastructures, traditional cloud computing domains (e.g., **OpenStack**), and resource-limited SOHO **home gateways**, properly extended to make it compatible with the FROG orchestrator. The set of capabilities and resources exported by each single domain, coupled with the constraints specified by the service itself (e.g., the IPsec client endpoint must stay on the tenant home gateway and not on the data centre) determines how the orchestrator splits the service graph, originating the proper set of sub-graphs that are deployed on the selected infrastructure domains.

The FROG orchestration architecture heavily relies on an intermediate message bus, which complements the traditional REST API to interconnect the different components.
This solution provides a clear advantage when the recipient of the information published is not known such as in the bootstrapping process, or when different components (e.g., service layer and orchestrator) need to know the same information coming from the infrastructure domains to perform their job.

The last block of the architecture is the preferred service layer @POLITO, called "*user-defined network functions*", which allows to deploy a complex service based on a set of events occurring in the infrastructure. However, the above service layer is only a proof of concept and can be replaced by other service layers, while the orchestration architecture is service-independent and is able to orchestrate whatever request coming as a service graph.

An overview of the architecture, with a possible usage example, is shown in the picture below.

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/frog-overview.png)


## Main FROG components

The FROG is made by different components, which are developed independently. Therefore this repository provides a single point of entry for all of them, which are available here as GIT submodules.

Particularly, the FROG includes the following components:
  * [FROG orchestrator](http://github.com/netgroup-polito/frog4-orchestrator/): overarching orchestrator, which controls different infrastructure domains.
  * [FROG OpenStack Domain Orchestrator](http://github.com/netgroup-polito/frog4-openstack-do/): domain orchestrator, it can control an OpenStack domain.
  * [FROG OpenFlow Domain Orchestrator](http://github.com/netgroup-polito/frog4-openflow-do/): domain orchestrator, it can control a pure OpenFlow domain (either using ONOS or OpenDayLight).
  * [FROG User-Defined Service Functions (Service Layer)](http://github.com/netgroup-polito/frog4-service-layer/): one of the possible service layer. This component deploys an _authentication graph_, forcing all the user devices (e.g., laptops) to be redirected to a captive portal to authenticate the user itself. When this happens, this component asks the orchestrator to deploy a user-specific graph and redirects all the user traffic to it.

In addition, the FROG orchestrator is compatible with the [Universal Node](http://github.com/netgroup-polito/un-orchestrator) as well, hence you can use that component to bring one (or more) compute nodes, such as home gateways, as part of the controlled infrastructure.

The FROG architecture relies on an intermediate message bus to interconnect the different components.
This solution provides a clear advantage when the recipient of the information published is not known such as in the bootstrapping process, or when different components (e.g., service layer and orchestrator) need to know the same information coming from the infrastructure domains to perform their job.
All FROG components expose also a REST API to play with, whose description is available (after installing the selected component) at the URL `http://{component_address}:{component_port}/apidocs/index.html`.

## Additional documentation
Additional documentation, such as installation instructions, etc, is available in the [docs](docs) folder. 
