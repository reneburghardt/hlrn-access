# A Guide for Accessing the GPUs Cluster at the NIM/HLRN HPC.

This is a guide for accessing the computational resources at NIM/HLRN. It is a condensed version of [this guide](https://www.hlrn.de/doc/display/PUB/GPU+Usage), which should be referred for further information.


## Hardware and Nodes
The NIM/HLRN HPC consists in a cluster of processing nodes, to which users can submit [SLURM](https://slurm.schedmd.com/documentation.html) jobs from access nodes within the same HPC system. For a complete list of resources, please referr to [this link](https://www.hlrn.de/doc/display/PUB/Compute+node+partitions#Computenodepartitions-Emmy(G%C3%B6ttingen)). 

## Creating an account
Please apply for an account here: https://www.hlrn.de/doc/display/PUB/Apply+for+a+User+Account.

You will also receive an e-mail with a PIN and username. Please store this PIN safely, since it is used to set your initial password when your application is accepted.

## Accessing the servers

The password that you set is for the portal only. In order to access the nodes on the HLRN cluster, you need to have an SSH key uploaded to the HLRN portal: https://www.hlrn.de/doc/display/PUB/SSH+Login. Notice that when you follow the instructions to upload an SSH key, you will receive an e-mail with the link to access the upload page, and you should upload just the _.pub_ file there.

With the SSH key uploaded, you can now log into the cluster using ssh: ```ssh -i <path_to_your_private_key> -l <your_username> glogin9.hlrn.de```. We recommend using `glogin9` instead of others because it allows you to access the `/scratch-grete/` directly from the assigned GPU machine.

## Testing the servers
Once in the server, to test its functionality you can follow the the tutorial available in https://gitlab-ce.gwdg.de/dmuelle3/deep-learning-with-gpu-cores. 


## Using the nodes with singularity, vscode and slurm

At the moment these instructions work with Linux and Mac. It is not tested on Windows.

In order to run code with your dependencies on a compute node you need three things in order: (1) create a singularity container (docker is not allowed), (2) run the singularity container on the node, and (3) install and setup vscode and connect to the running singularity container in order to develop on the assigned node.

### Creating the singularity container
Create a singularity image using a singularity definition file (find examples under `singularity/`). You can also convert a docker image into a singularity image: https://docs.sylabs.io/guides/3.0/user-guide/build_a_container.html. In order to build a singularity image, use: `singularity build --fakeroot <singularity_example.sif> <singularity_example.def>`.

Note that the image needs to be built on a Linux machine.

### Setup vscode on your local machine
Three steps: 

(1) Install vscode https://code.visualstudio.com on your local machine; 

(2) Install the extension `Remote - SSH`: https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh; 

(3) `ssh` into the login node `glogin9` via vscode. In order to do this, add a config file a provided in the repo (`config`) under `~/.ssh/config` and ssh into the login node via the `Command pallete` on vscode (keyboard shortcut `cmd + ^ + p` on mac) by using the option "Remote-SSH: Connect to Host...". Please add both the entries as in the config file provided, i.e., "Host hlrn ..." as well as "Host hlrn-*".

Doing this creates a `.vscode-server` folder on the login node that you will need in order to use vscode on your local machine in order to develop on the assigned gpu machine on the cluster.

### Running the singularity container on slurm
Once the singularity image is build, upload the image (`.sif` file) to the login node (e.g. to your home dir). In order to run the singularity image (i.e., create a container/instance): 

(1) create an `example.sbatch` file (follow example in `example.sbatch`), 

(2) you can use the `sbatch` command on the login node to run the `example.sbatch` file, for example: `sbatch example.sbatch`.
We recommend you familiarize yourself with slurm an slurm commands, which will help you see (a) what nodes are available, (b) how to check what is assigned to you, (c) how to read the output, (d) how to change the sbatch file in order to customize your requirements (such as needing more GPU nodes), etc. Please see the slurm documentation page for more: https://slurm.schedmd.com. 

### Remote-ssh'ing into the assigned compute node
(1) Once you run the `sbatch` command your built singularity image will run as an instance on your assigned GPU node. You can find out which one via the command: `squeue --me`. Copy the name of your assigned compute node `<compute-node-name>` listed under "NODELIST" (e.g. `ggpu111`). 

(2) You can now directly `remote ssh` into the instance running on the GPU node via vscode locally. In order to do this, open the `Command pallete` on vscode (keyboard shortcut `cmd + ^ + p` on mac) by using the option "Remote-SSH: Connect to Host..." and type in `hlrn-<compute-node-name>` and hit enter. This should let you login to your assigned compute node.

Credits to Pedro Costa Klein for helping create this document.