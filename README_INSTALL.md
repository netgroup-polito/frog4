# Installing the FROG

## Getting the code

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

## Installing and launching the FROG

Please follow the appropriate README files in each subfolder in order to compile and launch each component.
Please note that you are not required to use all the component at the same time, as this depends on your particular network/service setup.
Mandatory components are: the `orchestrator`, the `datastore`, at least one domain orchestrator, and the [Double Docker broker](https://github.com/Acreo/DoubleDecker).
Please note that, although domain orchestrators may ask to install the datastore and the broker, just one installation of those components is needed to make the FROG system working.
