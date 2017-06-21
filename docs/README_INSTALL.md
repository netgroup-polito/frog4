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

### FROG4-orchestrator

Follow the instructions provided in the file [README_INSTALL](https://github.com/netgroup-polito/frog4-orchestrator/blob/master/README_INSTALL.md).

Important considerations:
* in case you install the FROG4-orchestrator on the same machine where you installed the Broker, you do not have to clone again the [DoubleDeker](https://github.com/Acreo/DoubleDecker-py) repository. However, you have to apply the patch (as described in the FROG4-orchestrator repository) and install it again;
* when setting the configuration file, set this line https://github.com/netgroup-polito/frog4-orchestrator/blob/master/config/default-config.ini#L26 so that it points to the broker previously installed;
* when setting the configuration file, this line https://github.com/netgroup-polito/frog4-orchestrator/blob/master/config/default-config.ini#L53 must be set so that it points either to the Datastore previously installed, or to a new Datastore;
* when setting the configuration file, this line https://github.com/netgroup-polito/frog4-orchestrator/blob/master/config/default-config.ini#L28 must be set so that it points to a local file containing the key to be used on the message bus. Particulrarly, it must point to the file `public-keys.json` generated before. Of course, if you are installing the FROG4-orchestrator on a different machine than that running the broker, you have to copy the `public-keys.json` on the new machine.

### OpenStack domain orchestrator

Follow the instructions provided in the file [README_INSTALL](https://github.com/netgroup-polito/frog4-openstack-do/blob/master/README_INSTALL.md). 

### SDN domain orchestrator

Follow the instructions provided in the file [README_INSTALL](https://github.com/netgroup-polito/frog4-sdn-do/blob/master/README_INSTALL.md).

Important considerations:
* in case you install the FROG4-orchestrator on the same machine where you installed the Broker, you do not have to clone again the [DoubleDeker](https://github.com/Acreo/DoubleDecker-py) repository. However, you have to apply the patch (as described in the FROG4-orchestrator repository) and install it again;

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






