# dockemu-ansible
New implementation of Dockemu simulation framework based on Ansible

By default it will execute a CoAP environment with a configurable number of clients and one node playing the role of server. It uses the example apps provided by libocap.

## How to execute
1. Install Ansible
2. Define where to install Dockemu. It can be in the same host which is running Ansible but it is recommended to use a clean server.
3. Execute Ansible script with the *install* tag to install all the reuqired packages, ns3 and Docker:
`ansible-playbook dockemu.yml -t install`
4. Execute the Ansible script with the *prepare* tag to prepare all the containers, log folders and network interfaces: 
`ansible-playbook dockemu.yml -t prepare`
5. To perform the simulation itself call the Ansible script with the *execute* tag: 
`ansible-playbook dockemu.yml -t execute`
6. To clean the environment after the simulation use the *destroy* tag:
`ansible-playbook dockemu.yml -t destroy`

