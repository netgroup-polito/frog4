# The FROG (v.4)

![](https://raw.githubusercontent.com/netgroup-polito/frog4/master/images/frog.jpg)

## What is FROG?

The FROG is a software that is able to orchestrate NFV/cloud services across multiple heterogeneous domains.
The FROG is based on multiple domain orchestrators, each one responsible of a single infrastructure domain, that cooperate by timely exporting the capabilities and the available resources in each domains to an overarching orchestrator, which has to coordinate the deployment of the service across the entire infrastructure.
Supported domains include not only traditional infrastructures with network (e.g., OpenFlow only) or compute (e.g., OpenStack) capabilities, but also resource-limited SOHO home gateways, properly extended to make it compatible with the FROG orchestrator. The set of capabilities and resources exported by each single domain, coupled with the constraints specified by the service itself (e.g., the IPsec client endpoint must stay on the tenant home gateway and not on the data centre) determines how the orchestrator splits the service graph, originating the proper set of sub-graphs that are deployed on the selected infrastructure domains.

Furthermore, the FROG architecture relies on an intermediate message bus instead of using the traditional REST API to interconnect the different components.
This solution provides a clear advantage when the recipient of the information published is not known such as in the bootstrapping process, or when different components (e.g., service layer and orchestrator) need to know the same information coming from the infrastructure domains to perform their job.

An overview of the architecture, with a possible usage example, is shown in the picture below.

![](https://raw.githubusercontent.com/netgroup-polito/frog4/master/images/frog-overview.png)


## Which are the FROG components?

The FROG is made by different components, which are developed independently. Therefore this repository provides a single point of entry for all the available components, which are available here as GIT submodules.

Particulatly, the FROG includes the following components:
  * [FROG orchestrator] (https://github.com/netgroup-polito/frog4-orchestrator/): overarching orchestrator, which controls different infrastructure domains.
  * [FROG OpenFlow Domain Orchestrator] (https://github.com/netgroup-polito/frog4-openflow-do/): domain orchestrator, it can control a pure OpenFlow domain (either using ONOS or OpenDayLight).
  * [FROG User-Defined Service Functions - Service Layer] (https://github.com/netgroup-polito/frog4-service-layer/): one of the possible service layer. This component deploys an _authentication graph_, forcing all the user devices (e.g., laptops) to be redirected to a captive portal to authenticate the user itself. When this happens, this component asks the orchestrator to deploy a user-specific graph and redirects all the user traffic to it.

## Why FROG?

The FROG (Flexible and pROGrammable router) is a project started at the Computer Networks group ([Netgroup](http://netgroup.polito.it)) of ([Politecnico di Torino](http://www.polito.it)) back in 2012. Since then, the project evolved with different incarnations and has now become a cloud/NFV orchestrator. Hence, the original name, which includes the term 'router' is no longer appropriate, but we keep this name to remind us where this project comes from.

In a nutshell:
  * FROG v.1 (2012): first original idea, proof-of-concept implementation. OpenFlow was used to send the traffic to a controller, where all the processing was done.
  * FROG v.2 (2013-2014): second release of the idea, implemented through a dedicated platform, written from scratch. Impressive results with respect to scalability and agility, but not compatible with existing cloud services.
  * FROG v.3 (2014-2015): from a dedicated box to provide NFV services, we moved to an overarching orchestrator that is able to control different domains, in particular OpenStack. The service logic resides in the Orchestrator, while the data plane is implemented by OpenStack. The biggest problem is that OpenStack has been found not the best choice for network-intensive tasks, such as NFV services, nor for infrastructures distributed across a wide area domain.
  * FROG v.4 (2015-): the current incarnation of the project keeps the focus on an overarching orchestrator, but it adds the capability of controlling different heterogenous domains (e.g., OpenStack). FROG v4 (partially) moves back to the origins by adding a new component, called [Universal Node](http://github.com/netgroup-polito/un-orchestrator) (developed within the EU-sponsored FP7 [UNIFY](http://www.fp7-unify.eu) project), which is an optimized server (a.k.a. *compute node*) for cloud and NFV services. In addition, FROG v.4 uses a message bus to provide connectivity between the different components, which simplifies the routing of control messages. 
