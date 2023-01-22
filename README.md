The design of this role may not be following the best practice standards for a role so excuse my design :)

Role Name and Description 
=========================
Role Name: Kubernetes-init

This Role ensures that every managed host is configured for the following requirements:

1. Yum Local Repo
2. SELinux set to Permissive
3. Firewall is Disabled
4. No Swap is on the system
5. Modify /etc/hosts
6. Install CRI-O 
7. Install Kubelet Kubectl Kubeadm
8. Install HAProxy on a dedicated host
9. Install NFS-Server on a dedicated host
10. Initiate Master-node

Requirements
------------
***********************************************************************************************************
** It is required to put the "inventories" directory outside the role in your ansible project directory! **
***********************************************************************************************************

- community.general collection installed.
- moving inventories directory outside the role.

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.


Variables are mainly defined in:
--------------------------------

vars/main.yml
inventories/host_vars
inventories/group_vars

**** It is required to put the inventories directory outside the role in your ansible project directory! ****


--> "inventories" Directory Usage <-- 
-------------------------------------

Directory Hierarchy:
####################

inventories/
└── staging
    ├── group_vars
    │   ├── infrastructure.yaml
    │   ├── k8snodes.yaml
    │   ├── masternodes.yaml
    │   └── workernodes.yaml
    ├── hosts
    └── host_vars
        ├── myhaproxy.yaml
        ├── mymaster.yaml
        ├── mynfs.yaml
        └── myworker.yaml

-------------------------------------

hosts:
#######
[k8snodes]
mymaster
myworker
[masternodes]
mymaster
[workernodes]
myworker
[infrastructure]
mynfs
myhaproxy

-------------------------------------

group_vars/:
###########
- There are no variables defined yet in the group_vars

-------------------------------------
host_vars/:
##########

- Each host has his own variable file
- Each file contains the following variable for dynamic ip changing purposes

(EXAMPLE) Content of host_vars/mymaster.yaml
-->
   ansible_host: 192.168.9.50

-------------------------------------
Other variables are defined in the vars/main.yaml


{{ mount_point }} = Mount point of the local repository 
{{ crio_repo1 }} Source path for CRI-O Repo
{{ crio_repo2 }} = Source path for CRI-O Repo
{{ crio_repo1_path }} =  CRI-O Local Repo path
{{ crio_repo2_path }} = CRI-O Local Repo path
{{ nfs_share }} = Path of nfs exported directory and nfs mount point for other servers


Templates
---------

staging_hosts.j2 = populates list of hosts in /etc/hosts of all nodes
haproxy.j2 = configuration file of the haproxy


Files
-----
modules-k8s.conf  sysctl-k8s.conf

Contains k8s configurations on sysctl

Dependencies
------------

community.general collection

Install it using:

ansible-galaxy collection install community.general

Example Playbook
----------------
- name: Project Play
  hosts:
    - k8snodes
    - infrastructure

  roles:
    - Kubernetes-init

  pre_tasks:
    - name: Ping all hosts now
      ping:


  post_tasks:
      - name: Cluster Status
        debug:
          msg: "Cluster Implementation is Done!"
        when: inventory_hostname == 'mymaster' in groups.k8snodes


- It is adviced to use this playbook to run your role independantly.


Role Tasks
----------
- Every host has his own tasks in a specific yaml file. These tasks files are then imported into the main.yml tasks file of the role.

Tasks files used are as follow:
-->
allhoststasks.yaml  backup_main.yaml  haproxytasks.yaml  k8snodestasks.yaml  main.yaml  mastertasks.yaml  nfstasks.yaml  workertasks.yaml

Author Information
------------------
Written By:

Amr Sioufy 
Systems Engineer @ Linux-Plus Information Systems
