# The FROG (v.4) [(Wiki)] (https://github.com/netgroup-polito/frog4/wiki/)

## Live playground

For who is interested to play with this software without having to install everything, a live playground is available [here](https://github.com/netgroup-polito/frog4/wiki/Live-playground).

## Overview

The FROG is a NFV/cloud service orchestrator that supports multiple heterogeneous domains.

The FROG is made of several components.
Given that a complex infrastructure is composed by several domains (e.g., SDN/OpenFlow network, OpenStack cloud), each (homogeneous) infrastructure domain is handled by a dedicated FROG domain orchestrator.
**Domain orchestrators**, each one responsible of a single infrastructure domain, cooperate by timely exporting (i.e., _publishing_) the capabilities and the available resources to a **message bus**. 
Finally, an **overarching orchestrator**, sitting on the same bus, can receive the above information and can coordinate the deployment of the requested service across the entire infrastructure.

Supported domains include **SDN/OpenFlow** network infrastructures, traditional cloud computing domains (e.g., **OpenStack**), and resource-limited SOHO **home gateways**, properly extended to make it compatible with the FROG orchestrator. The set of capabilities and resources exported by each single domain, coupled with the constraints specified by the service itself (e.g., the IPsec client endpoint must stay on the tenant home gateway and not on the data centre) determines how the orchestrator splits the service graph, originating the proper set of sub-graphs that are deployed on the selected infrastructure domains.

The FROG orchestration architecture heavily relies on an intermediate **message bus**, which complements the traditional REST API to interconnect the different components.
This solution provides a clear advantage when the recipient of the information published is not known such as in the bootstrapping process, or when different components (e.g., service layer and orchestrator) need to know the same information coming from the infrastructure domains to perform their job.

The last block of the architecture is the preferred service layer @POLITO, called "*user-defined network functions*", which allows to deploy a complex service based on a set of events occurring in the infrastructure. However, the above service layer is only a proof of concept and can be replaced by other service layers, while the orchestration architecture is service-independent and is able to orchestrate whatever request coming as a service graph.

More information is available in the [FROG wiki] (https://github.com/netgroup-polito/frog4/wiki/).

![](https://raw.githubusercontent.com/netgroup-polito/frog4/master/images/frog.jpg)

