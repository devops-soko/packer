# MaaS

## 1. Related Knowledge
### 1) bare metal server
- row server without os or a single tenant server 
### 2) PXE(Preboot Execution Environment) 
- the environment to boot computer through network interface
### 3) KVM(Kernel-based Virtual Machine) 
- the virtualization module in the Linux kernel that allows the kernel to function as a hypervisor
### 4) IMPI(Intelligent Platform Management Interface)
- a set of computer interface specifications for hardware's remote management 


## 2. Maas Basic Knowledge
### 1) What is MaaS
- Bare metal Machine and Virtual Machine Provisioning Tools
- cf. Bare metal Machine and Virtual Machine can be recognized by using the same network band

### 2) MaaS's Structure (tiered architecture)
![maas_structure](https://user-images.githubusercontent.com/79248512/132089670-7065ac08-671b-4da4-91b7-21bdc6924750.png)

- region : central controller that manages rackd
- rackd : controller that manages multiple racks

### 3) Features
- MaaS provides only REST API
- High availability : when the connectiviey issue occure between a rackd and a rack, a regiond recognize it and change switch gateway to use another rackd 
- zone Independence : Servers can be distributed regardless of rack physical location or network segment
- Maas Manges Bare Metal with IPMI and PXE


### 4) MaaS Dashboard
![maas_dashboard](https://user-images.githubusercontent.com/79248512/132089775-c3ae1bbb-c8d9-4fcd-b48d-dbcff570c6c1.jpeg)



### 5) Maas Node Life Cycle
![Mass_node_status](https://user-images.githubusercontent.com/79248512/132089778-3b0f7772-06a6-4611-83fc-5b0d40306623.png)

- New: Exploring new PXE-bootable machines on the MAAS network
- Commissioning: The process that machine resources such as RAM, CPU, disks, NICs, etc. are being configured for use by MAAS
- Ready: The status that new nodes can be assigned to machines through MAAS
- Allocated: The status that the mahcine is allocated to user
- Deploying: Activating assigned node through the MAAS and installing the server OS(node status will be changed to 'Deloyed' after Deploying process is completed)
- Releasing: Deactivating the node through MAAS and initializing resources(node status will be changed to 'Ready' after Releasing process is completed)


## 3. Install and Deploy MaaS
```
sudo apt install maas
sudo apt install maas-region-controller
sudo apt install maas-rack-controller
sudo maas-rack register
sudo maas init 
```
- after maas installation -> DNS, ip range, etc. should be set in maas server
- if cloud-init error occurs then maas server needs cloud-init package (https://intl.cloud.tencent.com/ko/document/product/213/12587)



## 4. MaaS's directions for use
```
Step 1. To Do List before Node Additional  
    - add image to MAAS (import): if you create a VM and boot it without ISO image registeration, the image cannot be imported during PXE boot process so error occurs.
    
    - Configure bare metal or vm's Network interface 
    (DHCP, DNS, Gateway, Subnet, dynamic IP range, static IP range, etc.)

step 2. Add Node
    Method 1) Add classis: add the same type of nodes in batches.

    Method 2) Add machine : add only one node

    Method 3) Registration with PXE boot/Power: Create VM in MAAS cluster network
                                                PXE boot automatically
                                                (ubuntu is isntalled on its memroy to be recognized)
                                                node is added in MAAS
                                                
step 3. Prepare to use node
    commission: prepare node to be available to make node status ready(by PXE)
    acquire: give permissions
    deploy : install OS and provision servers according to pre-set network interface information(the os or hypervisors I want is installed by PXE)
```

## 5. MaaS CLI
### 1) Reference
https://maas.io/docs/snap/2.9/cli/maas-cli#:~:text=The%20maas%20command%20You%20can%20obtain%20the%20maas,to%20install%20this%20package%3A%20sudo%20apt%20install%20maas-cli

### 2) MAAS CLI Installation
```
sudo apt install maas-cli
```

### 3) Example of CLI
cf 1. $PROFILE : user id

cf 2.  $SYSTEM_ID : node id

- create user
```
#admin user
$ sudo maas createadmin --username=$PROFILE --email=$EMAIL_ADDRESS

#general user
$ maas $PROFILE users create username=$USERNAME \ 
email=$EMAIL_ADDRESS password=$PASSWORD is_superuser=0
```

- get API key
```
$ sudo maas apikey --username=$PROFILE > $API_KEY_FILE
```

- login
```
$ maas login $PROFILE $MAAS_URL - < $API_KEY_FILE
```

- print node info
```
# print all noes
$ maas $PROFILE nodes read


# print specific nodes
$ maas $PROFILE machines read hostname=node2


# print nodes and system id
$ maas $PROFILE machines read | jq '.[] | .hostname, .system_id'
```

- image upload
```
$ maas $PROFILE boot-resources create name='esxi/6.7' title='VMware ESXi 6.7' architecture='amd64/generic' filetype='ddgz' content@=vmware-esxi.dd.gz
```

- node commission
```
$ maas $PROFILE machine commission $SYSTEM_ID
```

- allocate node
```
$ maas $PROFILE machines allocate system_id=$SYSTEM_ID
```

- deploy node
```
normal deployment
$ maas $PROFILE machine deploy $SYSTEM_ID


kvm deployment
$ maas $PROFILE machine deploy $SYSTEM_ID install_kvm=True
```