# The FROG (v.4)

The FROG is a NFV/cloud servce orchestrator with support of multiple heterogeneous domains.
The FROG is based on multiple domain orchestrators, each one responsible of a single infrastructure domain, that cooperate by timely exporting the capabilities and the available resources in each domains to an overarching orchestrator, which has to coordinate the deployment of the service across the entire infrastructure.
Supported domains include SDN/OpenFlow network infrastructures, traditional cloud computing domains (e.g., OpenStack), and resource-limited SOHO home gateways, properly extended to make it compatible with the FROG orchestrator. The set of capabilities and resources exported by each single domain, coupled with the constraints specified by the service itself (e.g., the IPsec client endpoint must stay on the tenant home gateway and not on the data centre) determines how the orchestrator splits the service graph, originating the proper set of sub-graphs that are deployed on the selected infrastructure domains.

Furthermore, the FROG architecture relies on an intermediate message bus, which complements the traditional REST API to interconnect the different components.
This solution provides a clear advantage when the recipient of the information published is not known such as in the bootstrapping process, or when different components (e.g., service layer and orchestrator) need to know the same information coming from the infrastructure domains to perform their job.

More information can be found in the [FROGv4 wiki] (https://github.com/netgroup-polito/frog4/wiki/).

![](https://raw.githubusercontent.com/netgroup-polito/frog4/master/images/frog.jpg)

