# FROGv4 Live playground

This page provides an example of use of the FROG live playground available @POLITO and that allows to play with the FROG orchestrator without having to install all the modules.

# Physical setup
The physical setup of the playground is the following.

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/physical_setup.png)

This setup includes the global orchestrator and a single domain orchestrator that controls an OpenStack domain.
All the FROG components are installed on a single server (although the different modules can stay on different servers for scalability purposes) along with the other required components (VNF repository and message bus).

The OpenStack domain, instead, makes use of two servers hosting respectively the OpenStack controller and a compute node.
The OpenStack dashboad is directly accessible at the URL [http://130.192.225.121:9090/horizon/](http://130.192.225.121:9090/horizon/) using *demo*/*[hidden password]* as credentials. If the navigation on the dashboard is slow, we recommend the use of Mozilla Firefox.
In addition, the first server hosts also an OpenDayLight Beryllium, which is used to control the network: for instance, OpenStack Neutron relies on this component to create the network setup between VMs. In addition the FROG OpenStack Domain Orchestrator will directly exploit OpenDayLight when an endpoint (e.g., a GRE tunnel toward the external world) has to be created inside the OpenStack domain.
In case of necessity, the OpenDayLight Beryllium GUI can be reached at [http://130.192.225.121:8181/index.html](http://130.192.225.121:8181/index.html).

The setup requires both global and domain orchestrators to contact the VNF repository in order to get the *templates* of requested VNFs, while VNF *images* must be present in the OpenStack domain (in the Glance service, which is part of the controller).
To achieve this, they have been configured to use the VNF Repository at URL [http://130.192.225.107:8080](http://130.192.225.107:8080) in their respective configuration files. Then, templates have been loaded to the VNF-Repository using this command:

    curl -H "Content-Type: application/json" -X PUT -d @dhcp.json http://130.192.225.107:8080/v1/VNF/dhcp/

where *dhcp.json* is the template file and *dhcp* at the end of the URL is the ID of the template we are loading.
This operation has been done, adapting the previous command, for the [dhcp](https://raw.githubusercontent.com/netgroup-polito/VNF-repository/master/sample-templates/dhcp.json), [cirros_nodhcp](https://raw.githubusercontent.com/netgroup-polito/VNF-repository/master/sample-templates/cirros_nodhcp.json) and [cirros](https://raw.githubusercontent.com/netgroup-polito/VNF-repository/master/sample-templates/cirros.json) templates that are used in this demo.

We are now ready to start playing with the FROG architecture; the following sections present two possible examples, a very simple one to start, and a more complex one later.

**Important warning**: the live playground may have been used by some other users that forgot to delete their graphs. Therefore we strongly suggest to connect to the FROG orchestrator first (using the following URL: [http://130.192.225.107:9000/apidocs/index.html](http://130.192.225.107:9000/apidocs/index.html)) and list the graphs that are currently deployed in the infrastructure. In case you find that the examples below have already been deployed, either (a) delete the existing instances or (b) (better) deploy the above graphs using new graphIDs in order to avoid conflicts.


# Example 1
As a first step we want to deploy a simple graph composed of just two VNFs, connected to each other. 

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/ex_1.png)

According to the NFFG supported by the FROG, this graph is described by the following JSON.

```json
{
  "forwarding-graph": {
    "id": "101",
    "name": "Demo Graph 1",
    "VNFs": [
      {
        "id": "00000001",
        "name": "vnf1",
        "vnf_template": "cirros_nodhcp",
        "ports": [
          {
            "id": "inout:0"
          }
        ]
      },
      {
        "id": "00000002",
        "name": "vnf2",
        "vnf_template": "cirros_nodhcp",
        "ports": [
          {
            "id": "inout:0"
          }
        ]
      }
    ],
    "big-switch": {
      "flow-rules": [
        {
          "match": {
            "port_in": "vnf:00000001:inout:0"
          },
          "actions": [
            {
              "output_to_port": "vnf:00000002:inout:0"
            }
          ],
          "priority": 10,
          "id": "1"
        },
        {
          "match": {
            "port_in": "vnf:00000002:inout:0"
          },
          "actions": [
            {
              "output_to_port": "vnf:00000001:inout:0"
            }
          ],
          "priority": 10,
          "id": "2"
        }
      ]
    }
  }
}
```

This NF-FG defines two VNFs with a single port (`inout:0`), connected through two flowrules that send the traffic coming from the port of the VNF `00000001` to the port of the VNF `00000002` and vice versa. The VNFs use the `cirros_nodhcp` template. CirrOS is a minimal Linux distribution designed for use as a test image on clouds such as OpenStack Compute.

In order to instantiate this graph, you can either use the UNIX `curl` tool with the appropriate parameters in order to contact the appropriate REST API of the global orchestrator, or connect to the URL [http://130.192.225.107:9000/apidocs/index.html](http://130.192.225.107:9000/apidocs/index.html), which lists all the API supported by the global orchestrator and it allows to play with them through a minimal user interface.
In the latter case, you have to execute a `PUT` action and fill in all the fields that are shown in the GUI, using the JSON above and *demo*/*[hidden_password]*/*demo* (respectively for *user*, *password* and *tenant*) as authentication parameters.

To see this graph deployed you can login to the OpenStack dashboard with the above mentioned credentials, and go to the Compute -> Instances page. The instances names are `{VNF_ID}-{VNF_name}`, so they are `00000001-vnf1` and `00000002-vnf2`. In a CirrOS image, the login account is `cirros` and the password is `cubswin:)`. Please note that neither instances have the DHCP enabled, so a manual IP address has been configured in both of them. We assigned `10.1.1.1` to `00000001-vnf1` and `10.1.1.2` to `00000002-vnf2` and you can verify the connectivity from the dashboard.

# Example 2
In this example we introduce the concept of the *endpoint*, which was not present in the previous graph.
Endpoints, in this example, are used to connect multiple graphs together. In particular, we will create a service that includes a DHCP server and two users that get an IP address from it. This is done with 3 graphs:

1. the first one will deploy the DHCP server and an internal endpoint where the two users will connect to;
2. the second onw will deploy the first user connected to the DHCP;
3. the third one will do the same with the second user.

At the end, both users will have a valid IP address and they will ping each other because the internal endpoint is modelled as a virtual switch.

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/ex_2.png)


## Step 1: Instantiating a DHCP server

This graph can be represented by the following picture:

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/ex_2_1.png)

This corresponds to the following JSON description:

```json
{
  "forwarding-graph": {
    "id": "102_1",
    "name": "Demo Graph 2_1",
    "VNFs": [
      {
        "id": "00000003",
        "name": "dhcp",
        "vnf_template": "dhcp",
        "ports": [
          {
            "id": "inout:0"
          }
        ]
      }
    ],
    "end-points": [
      {
        "type": "internal",
        "internal": {
          "internal-group": "1",
          "node-id": "130.192.225.122"
        },
        "id": "00000001"
      }
    ],
    "big-switch": {
      "flow-rules": [
        {
          "match": {
            "port_in": "endpoint:00000001"
          },
          "actions": [
            {
              "output_to_port": "vnf:00000003:inout:0"
            }
          ],
          "priority": 10,
          "id": "1"
        },
        {
          "match": {
            "port_in": "vnf:00000003:inout:0"
          },
          "actions": [
            {
              "output_to_port": "endpoint:00000001"
            }
          ],
          "priority": 10,
          "id": "2"
        }
      ]
    }
  }
}
```

There is the DHCP VNF, the internal endpoint and two flowrules that deliver the traffic from the DHCP port and the endpoint and vice versa.
It is important to note the internal endpoint parameter called `internal-group`, which is used to stick together multiple graphs. In fact, when the FROG orchestrator detects two internal endpoints that have the same value of the `internal-group` parameter, graphs are connected together.

The DHCP server is called `00000003-dhcp` in the OpenStack dashboard. Its credentials are frog/frog. From the console you can see that the IP address of the `eth0` interface is `192.168.12.1`. The DHCP server assigns IP addresses in the range 192.168.12.100 - 192.168.12.200.



## Step 2: Adding User1

In this step will will deploy the following graph, which will be attached to the previous one through the internal endpoint:

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/ex_2_2.png)

This graph corresponds to the following JSON:

```json
{
  "forwarding-graph": {
    "id": "102_2",
    "name": "Demo Graph 2_2",
    "VNFs": [
      {
        "id": "00000003",
        "name": "user1",
        "vnf_template": "cirros",
        "ports": [
          {
            "id": "inout:0"
          }
        ]
      }
    ],
    "end-points": [
      {
        "type": "internal",
        "internal": {
          "internal-group": "1",
          "node-id": "130.192.225.122"
        },
        "id": "00000001"
      }
    ],
    "big-switch": {
      "flow-rules": [
        {
          "match": {
            "port_in": "endpoint:00000001"
          },
          "actions": [
            {
              "output_to_port": "vnf:00000003:inout:0"
            }
          ],
          "priority": 10,
          "id": "1"
        },
        {
          "match": {
            "port_in": "vnf:00000003:inout:0"
          },
          "actions": [
            {
              "output_to_port": "endpoint:00000001"
            }
          ],
          "priority": 10,
          "id": "2"
        }
      ]
    }
  }
}
```

This graph includes *user1* VNF, the internal endpoint and two flowrules that deliver the traffic from the VNF port to the endpoint and vice versa. The user1 VNF is called `00000003-user1` in the OpenStack dashboard. It is a `cirros` image and, unlike the `cirros_nodhcp` image of the previous example, it has the DHCP enabled. From the console you can see that the IP address of the `eth0` interface is `192.168.12.100`, the first available IP address given by the DHCP server.



## Step 3: Adding User2

This step completes the example by adding a second user to the existing graph, as depicted in the following figure:

![](https://raw.githubusercontent.com/wiki/netgroup-polito/frog4/images/ex_2_3.png)

This graph corresponds to the following JSON:

```json
{
  "forwarding-graph": {
    "id": "102_3",
    "name": "Demo Graph 2_3",
    "VNFs": [
      {
        "id": "00000003",
        "name": "user2",
        "vnf_template": "cirros",
        "ports": [
          {
            "id": "inout:0"
          }
        ]
      }
    ],
    "end-points": [
      {
        "type": "internal",
        "internal": {
          "internal-group": "1",
          "node-id": "130.192.225.122"
        },
        "id": "00000001"
      }
    ],
    "big-switch": {
      "flow-rules": [
        {
          "match": {
            "port_in": "endpoint:00000001"
          },
          "actions": [
            {
              "output_to_port": "vnf:00000003:inout:0"
            }
          ],
          "priority": 10,
          "id": "1"
        },
        {
          "match": {
            "port_in": "vnf:00000003:inout:0"
          },
          "actions": [
            {
              "output_to_port": "endpoint:00000001"
            }
          ],
          "priority": 10,
          "id": "2"
        }
      ]
    }
  }
}
```

Similar to the previous step, we have a *user2* VNF, the internal endpoint and two flowrules that deliver the traffic from the VNF port to the endpoint and vice versa.
The user2 VNF is called `00000003-user2` in the OpenStack dashboard. From the console you can see that the IP address of the `eth0` interface is `192.168.12.101`, the second available IP address given by the DHCP server.

The example is now completed and you can verify the connectivity between the 3 VNFs deployed in the previous steps by entering in the VNF console (through the OpenStack dashboard) and pinging one VNF from another.
In fact, all the graphs that define an internal endpoint with the same value (in this case `internal group = 1`) are connected to a virtual switch, which guarantees the connectivity between VNFs.

**Important warning**: before leaving the playground, remember to connect to the FROG orchestrator and delete your graphs, so that the playground stays clean for other users.


