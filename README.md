# dockemu-ansible
New implementation of Dockemu simulation framework based on Ansible

By default it will execute a CoAP environment with a configurable number of clients and one node playing the role of server. It uses the example apps provided by libocap.

The current version supports up to 240 clients and 16 server instances. Please note that the max number of instances will depend on the resources of the server hosting the Dockemu framework. 

## Requirements
Dockemu uses Ansible to install all the required software, setup the simulation scenario and launch the simulation itself. It is recommended to execute Ansible from a host different from the one where Dockemu (ns3 + Docker containers) is going to run. However, nothing prevents you from executing the Ansible playbook from the same host where Dockemu is going to be deployed.

### Requirements of Ansible host
1. **Ansible**: version 2.7 or higher (recommended)

### Requeriments Dockemu host
1. **OS**: Ubuntu 18.04 or higher (it is very likely to be compatible with lower versions and other linux distributions but it has not been tested yet).

## How to execute
1. Install Ansible (version >=2.7)
2. Define where to install Dockemu. It can be in the same host which is running Ansible but it is recommended to use a clean server.
3. Execute Ansible script with the *install* tag to install all the reuqired packages, ns3 and Docker:
```
ansible-playbook dockemu.yml -t install
```
4. Execute the Ansible script with the *prepare* tag to prepare all the containers, log folders and network interfaces: 
```
ansible-playbook dockemu.yml -t prepare
```
5. To perform the simulation itself call the Ansible script with the *execute* tag: 
```
ansible-playbook dockemu.yml -t execute
```
6. To clean the environment after the simulation use the *cleanup* tag:
```
ansible-playbook dockemu.yml -t cleanup
```

Please note that if the playbook is executed without tags then the cleanup role will be the last one to be executed and it will destroy the simulation and its results. 

### Configuring the Ansible host file
The procedure to configure the host is the standard for Ansible.

```
[dockemu]
192.168.56.101 ansible_ssh_user=dockemu  ansible_become_user=root ansible_become_pass=dockemu ansible_python_interpreter=/usr/bin/python3
```

## Intructions to create the Dockerfiles to simulate IoT client and server devices

Currently it is necessary to add delay of 30s before starting the server daemon or the client application to make sure all the networking configuration has been finished. This requirements might be unnecessary in the future thanks to-be-added improvements.

It is necessary to build an image for clients and another one for servers.

### Base image
Following Docker best practices we recommend to use Alpine as the base image to achive a footprint in the host OS as low as possible. It is also recommended the use of multistage in the image building to get a very light simulation image.


The MAC addresses of the `eth0` interfaces of server and client containers are automatically assigned by Dockemu to random values starting with `12:34`.

### IPv4
The IPv4 addresses of the `eth0` interfaces of server and client containers are automatically assigned by Dockemu within the ranges:
- the address space 10.12.[0-239].1/16 is for clients. It is assigned one 10.12.[0-239].1/24 network to each client container. The IPs within its network container range as supossed to be internal to the containers. 
- the address space 10.12.[240-255].1/16 is for servers. It is assigned one 10.12.[240-255].1/24 network to each server container.

### IPv6
The IPv6 addresses of the `eth0` interfaces of server and client containers are automatically assigned by Dockemu within the ranges:
- the address space 2001:777:1::[0-239].1/64 is for clients. 
- the address space 2001:777:1::[240-255].1/64 is for servers.

## How to execute command withint a container

It is possible to execute commands directly within the container. To do that you need to check the *container id* with the command:

```
dockemu@dockemu-1:~$ sudo docker container ps -a
```

And then you will be able to execute any command in the container:

```
dockemu@dockemu-1:~$ sudo docker exec -it 1f155a6575bd ping 10.12.0.240
```

For example, if you want to check the IPs assigned to each container you execute:

```
dockemu@dockemu-1:~$ sudo docker exec -it 1f155a6575bd ifconfig
