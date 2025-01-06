# Vyos Home-Lab setup

Notes getting vyos up and running

## VM
Make sure it has 2 interfaces one for WAN one for LAN/SDN network

## Boot
```
install image
```

## Ansible setup

prerequisites: `pipenv` , `sudo apt install pipenv` or `brew install pipenv`

With `pipenv` installed , run `pipenv install` then shell into the `pipenv` with `pipenv shell` install 
ansible requirements, `ansible-galaxy install -r requirements.yml`

Run the playbooks as normal eg `ansible-playbook vyos_base.yml`

## Initial manual config
```
set system host-name 'firewall'
set interfaces ethernet eth0 address '10.0.1.254/24' # SDN network
set interfaces ethernet eth1 address '192.168.1.252/24'# Local/WAN network
set interfaces ethernet eth1 address dhcp # WAN not tested
set protocols static route 0.0.0.0/0 next-hop 192.168.1.254 # Router gateway if not using ISP/dhcp
set service dns forwarding allow-from '10.0.1.0/24' # Allow dns forwarding from SDN
set service dns forwarding listen-address '127.0.0.1'
set service ssh listen-address '192.168.1.252' # Set ssh listen address and port
set service ssh port '22'
set system login user <username> authentication plaintext-password foo
set system login user <username> authentication public-keys <keyname> key 'key'
set system login user oli authentication public-keys <keyname> type 'ssh-ed25519'
delete system login user vyos
set system name-server 192.168.1.114 # Set DNS server
set system name-server 192.168.1.115 # Set DNS server
# Set up DCHP for devnet
set service dhcp-server shared-network-name devnet authoritative
set service dhcp-server shared-network-name devnet description 'testing'
set service dhcp-server shared-network-name devnet subnet 10.0.1.0/24 option default-router '10.0.1.254'
set service dhcp-server shared-network-name devnet subnet 10.0.1.0/24 option name-server '192.168.1.114'
set service dhcp-server shared-network-name devnet subnet 10.0.1.0/24 range 0 start '10.0.1.1'
set service dhcp-server shared-network-name devnet subnet 10.0.1.0/24 range 0 stop '10.0.1.10'
set service dhcp-server shared-network-name devnet subnet 10.0.1.0/24 subnet-id '1'

# Set up SNAT
set nat source rule 1 description 'devnet via eth1'
set nat source rule 1 outbound-interface name 'eth1'
set nat source rule 1 source address '10.0.1.0/24'
set nat source rule 1 translation address 'masquerade'

# Set up firewall

```
