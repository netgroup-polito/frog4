# How to install the FROG4 components

The FROG4 architecture needs several components to be installed and configured.
The following instructions have been tested on Ubuntu 15.10 and 16.04.

## Message bus (DoubleDecker Broker) [Mandatory]
The message bus (**broker**) used in the FROG4 architecture is the [DoubleDecker](https://github.com/Acreo/DoubleDecker) software.

### Install the DoubleDeker Broker

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

### Generate the keys 

To install the generator for the keys to be used on the message bus, execute the following commands:

    #install dependencies 
    $ sudo apt-get update
    $ sudo apt-get install python3-setuptools python3-nacl python3-zmq python3-urwid python3-tornado
    $ git clone https://github.com/Acreo/DoubleDecker-py.git
    $ cd DoubleDecker-py
    $ sudo python3 setup.py install
    
    # generate public/private keys
    $ cd ..
    $ mkdir dd-keys
    $ cd dd-keys
    # create keys for 4 tenants, public, tenant a, b, and c
    $ ddkeys.py

The utility will create the keys used by the broker (broker-keys.json) and by all FROG4 components (frog4-orchestrator and all domain orchestrators). You can use the public-keys.json file for all of them. The keys are stored in the files "broker-keys.json" and "public-keys.json".

## Datastore
The [Datastore](https://github.com/netgroup-polito/frog4-datastore/) can be used by all the FROG4 components to retrieve the VNF templates, images, service descriptions and more.
You may want to install a single Datastore used by all the FROG components (i.e., FROG orchestrator and the several domain orchestrators), or you may install a different Datastore for each different component; both the options are supported by the FROG4 framework.
You will specify the Datastore instance to be used by each compoent in their own configuration files (instructions will be provided in the specific repositories).

To install one (or more) Datastore instances and execute them, follow the instructions provided in the [Datastore repository](https://github.com/netgroup-polito/frog4-datastore/).

## FROG4 orchestrators
Now you have installed all the additional modules needed to properly run the FROG4 framework. It's time to install the frog4-orchestrator and at least one domain orchestrator. For this, please refer to the specific README_INSTALL file skipping the DoubleDecker section if you have followed the [message bus section](#message-bus) in this page. Before launching each component follow the next sections to tune-up the whole system.
- FROG4-orchestrator (**mandatory**): [README_INSTALL](https://github.com/netgroup-polito/frog4-orchestrator/blob/master/README_INSTALL.md)
- SDN domain orchestrator: [README_INSTALL](https://github.com/netgroup-polito/frog4-sdn-do/blob/master/README.md)
- OpenStack domain orchestrator: [README_INSTALL](https://github.com/netgroup-polito/frog4-openstack-do/blob/master/README_INSTALL.md)

Please follow the appropriate README files in each subfolder in order to compile and launch each component.
Please note that you are not required to use all the component at the same time, as this depends on your particular network/service setup.
Please note that, although domain orchestrators may ask to install the datastore and the broker, just one installation of those components is needed to make the FROG system working.

## WARNINGS

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






