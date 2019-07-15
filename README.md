# PrivEOS Automation

This Ansible repo will deploy a privEOS fully automatically. It's the recommended way to set up privEOS nodes as it's easy and fast and creates a deterministic and reproducible setup. Thanks to [BlockMatrixNetwork](https://github.com/BlockMatrixNetwork/eos-mainnet) for inspiring me to automate the process using Ansible.

## Install Ansible on the Control Machine

We're going to install ansible on the control machine. The control machine can be any machine (not the server itself) that you would like to use to set up the server, e.g. your work laptop. 

Installing ansible is as easy as:

```
sudo pip install ansible
```

For further info, check out the [official ansible doc](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-machine).

## Preparing the Server 
You need a server with the following hardware specs:

* 4 GB of RAM
* 20 GB of Storage
* Bare metal or Cloud Server is both fine

These specs are valid for the testnet and might be increased over time as usage increases or as new functionality is introduced.

This Ansible repo currently only works with Ubuntu and has been tested with Ubuntu 18.04 LTS and Ubuntu 18.10. It might work with slightly older or newer versions as well. 

There is no need to prepare anything special, just have OpenSSH installed and running. You need to have SSH public key authentication configured so you can login to the server without password. 

## Running the Installation
On your control machine (not on the server that we're going to set up):
    
    git clone https://github.com/rawrat/priveos-automation
    cd priveos-automation
    
Edit the ```inventory``` file and replace ```my.server.name``` with your host name. There needs to be a valid DNS record for this host name pointing to the public IP address of the server before we start.

Next, open ```group_vars/priveos.yml``` and fill in your personal values for all the variables. For the watchdogPrivateKey, please follow the [instructions here](https://github.com/rawrat/privEOS#add-watchdog-permission).

The user you would like to use to login into the server can be given in the `ansible_user` variable. If you are using a non-privileged user to login, make sure this user can do sudo without a password.

If the SSH identity you would like to use is not configured in your ssh config, you can use the variable ```ansible_ssh_private_key_file``` to specify the location of your ssh key. If you are using a non-standard SSH port, you can set it in the variable ```ansible_port```.

Once that's done, we can start the installation like this:
    
    # run this from the top level folder of priveos-automation 
    ansible-playbook install.yml
    
This will take ~15-20 minutes. If everything worked, you should see an output like this in the end:

```
PLAY RECAP ***********************************************************************************
my.server.name    : ok=26   changed=25   unreachable=0    failed=0 
```

## Post Installation Setup
Now we need to import our private key into the secure vault. Please [continue here](https://github.com/rawrat/priveos-encryption-service#usage).

## What's next?
You can now move to the [final step](https://github.com/rawrat/privEOS#registering-your-node)

## Securing the server
The steps in this automation script are the minimal steps needed to install privEOS. You still need to harden your server setup yourself according to your own usual practices (enabling SELinux, Firewalls, secure system configuration, limiting SSH access, etc). In a production environment, never run IPFS on the same server. Always install IPFS on a server that is separate from privEOS.

