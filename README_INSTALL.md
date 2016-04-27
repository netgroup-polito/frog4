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

## Installing and launching the FROG

Please follow the appropriate README files in each subfolder in order to compile and launch each component.
Please note that you are nor required to use all the component at the same time, as this depends on your particular network/service setup.
