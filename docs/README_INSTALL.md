# How to install the FROG4 components

The FROG4 architecture needs several components to be installed and configured.
The following instructions have been tested on Ubuntu 15.10 and 16.04.

## Message bus (DoubleDecker Broker)
The message bus (**broker**) used in the FROG4 architecture is the [DoubleDecker](https://github.com/Acreo/DoubleDecker) software.

### Install the DoubleDeker Broker

Clone the code and install the broker (together its dependencies):

    $ git clone https://github.com/Acreo/DoubleDecker.git
    $ cd DobuleDecker
    $ sudo apt-get install wget build-essential libtool  automake pkg-config libjson0-dev libczmq-dev liburcu-dev libsodium-dev 	
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
    $ ddkeys.py

The utility will create the keys used by the broker (broker-keys.json) and by all FROG4 components (frog4-orchestrator and all domain orchestrators). The keys are stored in the files `broker-keys.json` and `public-keys.json`.

## Datastore
The [Datastore](https://github.com/netgroup-polito/frog4-datastore/) can be used by all the FROG4 components to retrieve the VNF templates, images, service descriptions and more.
You may want to install a single Datastore used by all the FROG components (i.e., FROG orchestrator and the several domain orchestrators), or you may install a different Datastore for each different component; both the options are supported by the FROG4 framework.
You will specify the Datastore instance to be used by each compoent in their own configuration files (instructions will be provided in the specific repositories).

To install one (or more) Datastore instances and execute them, follow the instructions provided in the [Datastore repository](https://github.com/netgroup-polito/frog4-datastore/).

## FROG4 orchestrators
Now you have installed all the additional modules needed to properly run the FROG4 framework. It's time to install the frog4-orchestrator and at least one domain orchestrator. For this, please refer to the specific README_INSTALL in each repository. Before launching each component follow the next sections to tune-up the whole system.

#### Users
Each orchestrator (i.e., FROG orchestrator, SDN domain orchestrator, OpenStack domain orchestrator, universal node)  has its own database that, among other information, contains the users enabled to interact with the orchestrator itself (i.e., their username and password).
Then, while deploying the orchestrators that are part of the FROG architecture, please add the same user(s) in each one of the databases.
To this purpose, you can follow the instructions provided in each repository.

### FROG4 web GUI

It is possibile to add a [GUI](https://github.com/netgroup-polito/fg-gui) on top of the FROG4-orchestrator and of each domain orchestrator (note that you have to use a differert GUI instance for each component).
The GUI can be used to:
* draw a service graph (on load it from an external file) and deploy it through the interaction with the underlying orchestrator
* view and modify service graphs already deployed, through teh interaction with the underlying orchestrator
* upload NF images and templates in the Datastore.

To install one (or more) FROG web GUI, follow the instructions provided in the [FROG4 web GUI repository](https://github.com/netgroup-polito/fg-gui/blob/master/README_INSTALL.md).

Important considerations:
* when setting the configuration file, these lines https://github.com/netgroup-polito/fg-gui/blob/master/config/default-config.ini#L22-L23 and https://github.com/netgroup-polito/fg-gui/blob/master/config/default-config.ini#L28-L29 must be set to point either to the Datastore previously installed, or to a new Datastore;

### FROG4-orchestrator

Follow the instructions provided in the file [README_INSTALL](https://github.com/netgroup-polito/frog4-orchestrator/blob/master/README_INSTALL.md).

Important considerations:
* in case you install the FROG4-orchestrator on the same machine where you installed the Broker, you do not have to clone again the [DoubleDeker](https://github.com/Acreo/DoubleDecker-py) repository. However, you have to apply the patch (as described in the FROG4-orchestrator repository) and install it again;
* when setting the configuration file, set this line https://github.com/netgroup-polito/frog4-orchestrator/blob/master/config/default-config.ini#L26 so that it points to the broker previously installed;
* when setting the configuration file, this line https://github.com/netgroup-polito/frog4-orchestrator/blob/master/config/default-config.ini#L28 must be set so that it points to a local file containing the key to be used on the message bus. Particulrarly, it must point to the file `public-keys.json` generated before. Of course, if you are installing the FROG4-orchestrator on a different machine than that running the broker, you have to copy the `public-keys.json` on the new machine;
* when setting the configuration file, this line https://github.com/netgroup-polito/frog4-orchestrator/blob/master/config/default-config.ini#L53 must be set so that it points either to the Datastore previously installed, or to a new Datastore.

### OpenStack domain orchestrator

Follow the instructions provided in the file [README_INSTALL](https://github.com/netgroup-polito/frog4-openstack-do/blob/master/README_INSTALL.md). 

Important considerations:
* in case you install the OpenStack domain orchestrator on the same machine where you installed the Broker, you do not have to clone again the [DoubleDecker](https://github.com/Acreo/DoubleDecker-py) repository. However, you have to apply the patch (as described in the OpenStack domain orchestrator repository) and install it again;
* when setting the configuration file, set this line https://github.com/netgroup-polito/frog4-openstack-do/blob/master/config/default-config.ini#L68 so that it points to the broker previously installed;
* when setting the configuration file, this line https://github.com/netgroup-polito/frog4-openstack-do/blob/master/config/default-config.ini#L69 must be set so that it points to a local file containing the key to be used on the message bus. Particulrarly, it must point to the file `public-keys.json` generated before. Of course, if you are installing the FROG4-orchestrator on a different machine than that running the broker, you have to copy the `public-keys.json` on the new machine;
* the OpenStack domain orchestrator uses the the Datastore to keep the network function templates. Then, you have to set this line https://github.com/netgroup-polito/frog4-openstack-do/blob/master/config/default-config.ini#L98 so that it points to the Datastore previously installed.

### SDN domain orchestrator

Follow the instructions provided in the file [README_INSTALL](https://github.com/netgroup-polito/frog4-sdn-do/blob/master/README_INSTALL.md).

Important considerations:
* in case you install the SDN domain orchestrator on the same machine where you installed the Broker, you do not have to clone again the [DoubleDecker](https://github.com/Acreo/DoubleDecker-py) repository. However, you have to apply the patch (as described in the SDN domain orchestrator repository) and install it again;
* when setting the configuration file, enable the connection to the broker (line https://github.com/netgroup-polito/frog4-sdn-do/blob/master/config/default-config.ini#L79) and set this line https://github.com/netgroup-polito/frog4-sdn-do/blob/master/config/default-config.ini#L83 so that it points to the broker previously installed;
* when setting the configuration file, this line https://github.com/netgroup-polito/frog4-sdn-do/blob/master/config/default-config.ini#L86 must be set so that it points to a local file containing the key to be used on the message bus. Particulrarly, it must point to the file `public-keys.json` generated before. Of course, if you are installing the FROG4-orchestrator on a different machine than that running the broker, you have to copy the `public-keys.json` on the new machine;
* the SDN domain orchestrator does not use the FROG4 datastore. Information about available NFs are retrieve directly from the underlying SDN controller (i.e., ONOS).

### WARNINGS

#### Domain description
Each domain orchestrator maintains and publishes a description of its resources. The description includes the nodes/interfaces of each domain that may be used to reach other domains, including the supported technologies (e.g, GRE tunnels, VLAN). When a domain orchestrator sends the description on the message bus for the first time, the FROG orchestrator becomes aware of such domain and learns how to contact it. Resources descriptions examples can be found in the config directory of each domain orchestrator repository. 
Particularly, it is important to set the domain orchestrator IP and port in the *management-address* field, to choose a domain name in the *name* field and to describe each interface. These information will be used by the FROG orchestrator to eventually split and deploy over multiple domains an incoming service graph.

# Running

In order to start the FROG framework, run the components previously installed in the following order:

- Launch the message broker:
   - Change the parameters according to your needs.

            $ ddbroker -k broker-keys.json -r tcp://127.0.0.1:5555 -s 0/0/0
            
 - Launch the Datastore as described [here](https://github.com/netgroup-polito/frog4-datastore/blob/master/README.md#run-it)
            
- Launch the FROG orchestrator as described [here](https://github.com/netgroup-polito/frog4-orchestrator/blob/master/README_INSTALL.md#run-the-orchestrator)

- Launch the domain orchestrator(s) and the associated GUIs, following the appropriate section in the respective README_INSTALL file.






