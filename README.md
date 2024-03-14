# Homelab Ansible
Ansible playbooks for setting up my RPi homelab with k3s and Tailscale


# Helpful CLI Flags
- -k: Request the password for the specified used before executing
- -K: requests the "become" password before executing
- -u <user>: Specify the user to use when executing the commands


## Validating the inventory

The inventory can be validate by running:
`ansible-inventory -i inventory.yaml --list`


## Ping Servers

The hosts can be pinged by running:
`ansible homelab -m ping -k -i inventory.yaml`

## Run a playbook

`ansible-playbook -u root -kK -i inventory.yaml playbooks/nas.yaml`


