# Ansible Playbook to setup LDAP on Ubuntu


## Installation

# Initial Steps

    * Ensure the repos are refreshed and all installed packages are updated
      $ sudo apt-get update && sudo apt-get upgrade -y

    * Reboot the server
      $ sudo reboot

    * Login again as Ubuntu to the server, and enter the root prompt with the command below:
      $ sudo -I

    * Generate the SSH keys (Go with the defaults)

      # ssh-keygen -t rsa

    * Enable password less login for the ubuntu user
      # visudo

    * Add the following line to the file
      ubuntu ALL=(ALL) NOPASSWD:ALL

    * Exit the root prompt and as the ubuntu user; For keygen, use the defaults
      $ ssh-keygen -t rsa
      $ ssh-copy-id ubuntu@localhost (This will prompt you for the ubuntu user password)


# Install ansible

  $ sudo apt-get update
  $ sudo apt-get install software-properties-common
  $ sudo apt-add-repository ppa:ansible/ansible
  $ sudo apt-get update
  $ sudo apt-get install ansible

# Install PHP modules
  $ sudo apt-get install php php-ldap php-gd php-json php-imagick apache2
  $ sudo apt-get -f install


* Clone the repository, replace <institution> with the actual name of your institution e.g muk, kyu, renu, umu

        git clone  https://github.com/ubuntunet/unIDa_LDAP.git <institution>-ldap
        cd <institution>-ldap

### Inventory File

Create the inventory file for your institution, for more information: http://docs.ansible.com/ansible/intro_inventory.html

        cp inventories/template inventories/<institution>

Open the inventory file with your favorite editor and change the ansible_host and ansible_user to your server environment. Don't forget to again replace <institution>.

### Variables File

Create the variables file for your institution, more information: http://docs.ansible.com/ansible/playbooks_variables.html

        cp group_vars/template group_vars/<institution>

Open the variable files in your favorite editor and adapt the values to your setup.


### Secrets File

Some values - passwords, credentials - are sensitive and should never be submitted to the Github repository. They are therefore stored in a file called secrets.yml, which is being ignored by Github.

Create the secrets.yml file

        cp group_vars/secrets.yml.example group_vars/secrets.yml

Open the secrets.yml file and add the sensitive values.

There are many ways to create random passwords/passphrases/salt, I prefer to use openssl for this task. You can replace 12 with a higher number for longer strings.

        openssl rand -base64 12


### Run the playbook

        ansible-playbook -i inventories/<institution> ldap.yml



### Open Ports on Firewall

The following ports need to be in order for LDAP/S to work properly:
```
TCP 389
TCP 636
```

----


## Activate LDAP Account Management (LAM)

LAM is a webfront end for LDAP with a rich feature set (https://www.ldap-account-manager.org/)

* Enable the 'lam' role by uncommenting it in ldap.yml
* (Re)Play the playbook
* Go to https://{{ fqdn}}



## Trouble Shooting

If you changed your rootpw, you need to remove the slapd service on the server manually for the new rootpw to be picked up. This has also helped me in other hopeless situations.

```
sudo aptitude purge slapd
sudo rm /etc/ldap/rootdn_created
sudo rm /root/.entriesadded
```
