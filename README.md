# Homelab Ansible

Ansible playbooks for setting up my RPi homelab with k3s and Tailscale. I borrow liberally from the [k3s/k3s-ansible](https://github.com/k3s-io/k3s-ansible) repo for configuring my k3s cluster with ansible. The purpose of creating this repo is to maintain things outside of k3s and to get familiar with writing ansible on my own.


# Helpful CLI Flags
- -k: Request the password for the specified used before executing
- -K: requests the "become" password before executing
- -u <user>: Specify the user to use when executing the commands

## Prerequisites

### Install Ansible

- On macOS:
  ```
  brew install ansible
  ```

- On Ubuntu/Debian:
  ```
  apt-get install ansible
  ```


### Install sshpass
To use SSH password authentication with Ansible (the `-k` flag), you need to install `sshpass`:

- On macOS:
  ```
  brew install sshpass
  ```

- On Ubuntu/Debian:
  ```
  apt-get install sshpass
  ```


## Validating the inventory

The inventory can be validate by running:
`ansible-inventory -i inventory.yaml --list`


## Ping Servers

The hosts can be pinged by running:
`ansible homelab -m ping -k -i inventory.yaml`

## Run a playbook

`ansible-playbook -u root -kK -i inventory.yaml playbooks/nas.yaml`

## Adding Additional Control Plane Nodes

To add additional K3s control plane nodes:

1. Add the new node to the `master_nodes` section in the inventory.yaml file
2. Run only the additional server role with one of these approaches:

   Option A: Directly specify the additional node name(s) exactly as they appear in your inventory:
   ```
   ansible-playbook -i inventory.yaml playbooks/install.yaml --tags k3s_server -K --limit storm_father
   ```
   
   Or to target multiple additional nodes:
   ```
   ansible-playbook -i inventory.yaml playbooks/install.yaml --tags k3s_server -K --limit 'storm_father,the_tower'
   ```

   Option B: Exclude the first node explicitly:
   ```
   ansible-playbook -i inventory.yaml playbooks/install.yaml --tags k3s_server -K --limit 'master_nodes:!bondsmith'
   ```

   Option C: Create a separate group in your inventory.yaml file for additional master nodes:
   ```yaml
   # Add this to inventory.yaml
   additional_masters:
     hosts:
       storm_father:
       the_tower:
   ```
   
   Then target that group:
   ```
   ansible-playbook -i inventory.yaml playbooks/install.yaml --tags k3s_server -K --limit additional_masters
   ```

These commands will apply the k3s_additional_server role to your additional control plane nodes.


