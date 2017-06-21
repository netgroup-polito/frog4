# How to install the FROG4 components

The FROG4 architecture needs several components to be installed and configured.
The following instructions have been tested on Ubuntu 15.10 and 16.04.

## Message bus (DoubleDecker Broker) 
The message bus (**broker**) used in the FROG4 architecture is the [DoubleDecker](https://github.com/Acreo/DoubleDecker) software.

Clone the code and install the broker (together its dependencies):

    $ git clone https://github.com/Acreo/DoubleDecker.git
    $ cd DobuleDecker
    $ sudo apt-install wget build-essential libtool  automake pkg-config libjson0-dev libczmq-dev liburcu-dev libsodium-dev 	
    $ mkdir -pv m4 
    $ autoreconf --force --install 
    $ ./configure
    $ make
    $ sudo make install
    $ sudo ldconfig

Generate the keys with ddkeys.py (inside the python/build/scripts-3.4 folder) giving an empty list of customers when requested:

    $ sudo python3.4 ddkeys.py

The utility will create the keys used by the broker (broker-keys.json) and by all FROG4 components (frog4-orchestrator and all domain orchestrators). You can use the public-keys.json file for all of them.

## Datastore
The [Datastore](https://github.com/netgroup-polito/frog4-datastore/) can be used by all the FROG4 components to retrieve the VNF templates, images, service descriptions and more.
You may want to install a single Datastore used by all the FROG components (i.e., FROG orchestrator and the several domain orchestrators), or you may install a different Datastore for each different component; both the options are supported by the FROG4 framework.
You will specify the Datastore instance to be used by each compoent in their own configuration files (instructions will be provided in the specific repositories).

To install one (or more) Datastore instances, follow the instructions provided in the [Datastore repository](https://github.com/netgroup-polito/frog4-datastore/).

### FROG4 components
Now you have installed all the additional modules needed to properly run the FROG4 architecture. It's time to install the frog4-orchestrator and at least a domain orchestrator. For this, please refer to the specific README_INSTALL file skipping the DoubleDecker section if you have followed the [message bus section](#message-bus) in this page. Before launching each component follow the next sections to tune-up the whole system.
- Global orchestrator: [README_INSTALL](https://github.com/netgroup-polito/frog4-orchestrator/blob/master/README_INSTALL.md)
- OpenFlow domain orchestrator: [README_INSTALL](https://github.com/netgroup-polito/frog4-openflow-do/blob/master/readme-install.md)
- OpenStack domain orchestrator: [README_INSTALL](https://github.com/netgroup-polito/frog4-openstack-do/blob/master/README_INSTALL.md)

Please follow the appropriate README files in each subfolder in order to compile and launch each component.
Please note that you are not required to use all the component at the same time, as this depends on your particular network/service setup.
Mandatory components are: the `orchestrator`, the `datastore`, at least one domain orchestrator, and the [Double Docker broker](https://github.com/Acreo/DoubleDecker).
Please note that, although domain orchestrators may ask to install the datastore and the broker, just one installation of those components is needed to make the FROG system working.

### Getting the code of the core modules

In order to get the code, you have to clone this repository _and_ all the submodules, which contains the different FROG components.
This lead to the necessity to clone them in the right folders, under the FROG4 orchestrator root.
For this, please follow the steps below:

        git clone https://github.com/netgroup-polito/frog4.git
        cd frog4
        git submodule init && git submodule update

At the end of the process you will find all the source code of the FROG4 composing components.
You can move in the independent subfolders and follow the instructions in there to compile and launch them.

Please note that the `submodule update` command retrieves a specific version of the given submodule, which may be well behind the latest commit. This is to guarantee that the code in the submodule is aligned and works with the rest of the system.
Therefore, if you are a programmer and you plan to modify the source code of a specific submodule, please make sure to upgrade the submodule to the latest version first.

#### Users
Each orchestrator has its own database and in the repositories there are SQL dumps that include some example users. In this way it is easy to add your own users, paying attention that the same credentials (username, password, tenant) must be present in each database. This is necessary because the global orchestrator forwards the credentials that arrive on its northbound API to the underlying domain orchestrators.

#### Domain description
Each domain orchestrator maintains and publishes a description of its resources. The description includes the nodes/interfaces of each domain that may be used to reach other domains, including the supported technologies (e.g, GRE tunnels, VLAN). When a domain orchestrator sends the description to the message bus for the first time the global orchestrator becomes aware of such domain and learns how to contact it. Resources descriptions examples can be found in the config directory of each domain orchestrator repository. It is important to set the domain orchestrator IP and port in the *management-address* field, to choose a domain name in the *name* field and to describe each interface. These information will be used by the global orchestrator to eventually split and deploy over multiple domains an incoming service graph.

## Running
- First of all you have to launch the message broker:
   - Change the parameters according to your needs.

            $ ddbroker -k broker-keys.json -r tcp://127.0.0.1:5555 -s 0/0/0
- Launch the global orchestrator: [How to run](https://github.com/netgroup-polito/frog4-orchestrator/blob/master/README_INSTALL.md#run-the-orchestrator)
- Launch the domain orchestrator(s) following the appropriate section in the respective README_INSTALL file.






