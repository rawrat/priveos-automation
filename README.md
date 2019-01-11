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

Next, open ```group_vars/priveos.yml``` and fill in your personal values for all the variables. The user you would like to use to login into the server can be given in the `ansible_user` variable. If you are using a non-privileged user to login, make sure this user can do sudo without a password.

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
Log in to the server 

    su - encryptionservice
    cd priveos-encryption-service

Edit config.js and insert your private key here. 

The file looks like this:
```
module.exports = {
  listenPort: '6000',
  currentPrivateKey: "__insert_your_private_key_here__",
  /**
    * If you're rotating your node key (above), keep a list of all the used 
    * keys here, please. Otherwise, old files that were encrypted with the 
    * old keys will be lost.
    */
  oldKeys: [
  ]  
}
```
Please replace ```__insert_your_private_key_here__``` with your private key.

The private key you enter here should match the one public key you are going to supply when calling ```priveosrules:regnode```. It should be different from your active and owner key. You should generate a new key just for this purpose. It does not need to be added as a permission to your account.

When you're done editing, it should look like in this example (please don't use this example key):

```
module.exports = {
  listenPort: '6000',
  currentPrivateKey: "5KEcQbZ8QPuwNfTWQRyNi12HMB5reAmCgigBXLPep7jp8eegi8k",
  /**
    * If you're rotating your node key (above), keep a list of all the used 
    * keys here, please. Otherwise, old files that were encrypted with the 
    * old keys will be lost.
    */
  oldKeys: [
  ]  
}
```

When you're done, please restart the encryptionservice:
  
    pm2 restart all
  

This key can be rotated by calling ```regnode``` again. A full history of all old private keys must, however be kept in this config file, otherwise, old files cannot be decrypted anymore and will be lost. 

## What's next?
You can now move to the [final step](https://github.com/rawrat/privEOS#registering-your-node)
