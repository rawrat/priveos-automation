# PrivEOS Automation

This Ansible repo will deploy a privEOS fully automatically. It's the recommended way to set up privEOS nodes as it's easy and fast and creates a deterministic and reproducible setup.

## Install Ansible on the Control Machine

We're going to install ansible on the control machine. The control machine is usually your work laptop. It's not necessary to install anything on the server other than the base Ubuntu 18.04.

Installing ansible is as easy as:

sudo pip install ansible

For further info, check out the [official ansible doc](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-machine).

## Preparing the Server 
You need a server with the following hardware specs:

* 4 GB of RAM
* 20 GB of Storage
* Bare metal or Cloud Server is both fine

These specs are valid for the testnet and might be increased over time as usage increases or as new functionality is introduced.

This Ansible repo currently only works with Ubuntu and has been tested with Ubuntu 18.04 LTS. It might work with slightly older or newer versions as well. 

There is no need to prepare anything special, just have OpenSSH installed and listening on the usual port. You need to have root login enabled and the SSH Key of your control machine should be in the .ssh/authorized_keys so we can login as root using only the Key.

## Running the Installation
    
    git clone https://github.com/rawrat/priveos-automation
    cd priveos-automation
    
Edit the inventory file and replace ```my.server.name``` with your host name. There needs to be a valid DNS record for this host name pointing to the public IP address of the server before we start.

Next, open ```group_vars/priveos.yml``` and fill in your personal values for all the variables.

Once that's done, we can start the installation like this:
    
    ansible-playbook priveos.yml
    
This will take ~15-20 minutes. If everything worked, you should see an output like this in the end:

```
PLAY RECAP ***********************************************************************************
my.server.name    : ok=26   changed=25   unreachable=0    failed=0 
```

## Post Installation Setup
Log in to the server 

    su - encryptionservice
    cd priveos-encryption-service

Edit config.js and insert your private key here. 

The private key you enter here is the one you are going to supply when calling ```priveosrules:regnode```. It should be different from your active and owner key. You should generate a new key just for this purpose. It does not need to be added as a permission to your account.

When you're done, please restart the encryptionservice:
  
    pm2 restart all
  

This key can be rotated by calling ```regnode``` again. A full history of all old private keys must, however be kept in this config file, otherwise, old files cannot be decrypted anymore and will be lost. 
