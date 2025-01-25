# FRR Configuration Deployment with Ansible

```bash
# Step 1: Clone the repository to your local machine
git clone <repository_url>
cd dn42

# Step 2: Edit the inventory file to define the target routers
# The inventory file contains the list of routers Ansible will manage.
# Example:
[routers]
edge-rtr-01 ansible_host=<IP_ADDRESS_OF_EDGE_RTR_01> ansible_user=root
edge-rtr-02 ansible_host=<IP_ADDRESS_OF_EDGE_RTR_02> ansible_user=root

# Step 3: Configure router-specific settings in the Ansible variables file
# Modify the roles/frr/vars/main.yml file to include router-specific BGP configuration settings,
# such as neighbor IPs, remote ASNs, and any network-specific attributes.
# Example:
routers:
  edge-rtr-01:
    hostname: edge-rtr-01
    home_neighbor:
      ip: 192.168.255.1
      asn: 65001
    edge_neighbor:
      ip: 169.254.1.2
      asn: 4242420513
      description: EDGE-02
    dn42_neighbor:
      ip: 172.20.53.103
      asn: 4242423914
      description: DN42
  edge-rtr-02:
    hostname: edge-rtr-02
    home_neighbor:
      ip: 192.168.254.1
      asn: 65001
    edge_neighbor:
      ip: 169.254.1.1
      asn: 4242420513
      description: EDGE-01
    dn42_neighbor:
      ip: 172.20.53.98
      asn: 4242423914
      description: DN42_KIOUBIT

# Step 4: Deploy the configuration to all routers
# Use the playbook to apply the configuration defined in the templates and variables
ansible-playbook -i inventory playbook.yml

# Step 5: Deploy the configuration to a specific router
# The --limit option allows targeting a single router for configuration updates
ansible-playbook -i inventory playbook.yml --limit edge-rtr-01

# Step 6: Verify the configurations
# Confirm that the configurations have been applied correctly on the routers by
# checking the /etc/frr/frr.conf file on each device and using BGP show commands.
# Example verification commands:
cat /etc/frr/frr.conf
vtysh -c "show ip bgp summary"
vtysh -c "show ip bgp neighbors <neighbor_ip> advertised-routes"

# Example Project Structure:
ansible/
├── inventory                      # Inventory file with router details
├── playbook.yml                   # Main Ansible playbook
├── roles/
│   ├── frr/
│   │   ├── tasks/
│   │   │   ├── main.yml           # Tasks to copy configuration and restart service
│   │   ├── templates/
│   │   │   ├── frr.conf.j2        # Jinja2 template for generating FRR configurations
│   │   ├── vars/
│   │   │   ├── main.yml           # Router-specific variables

