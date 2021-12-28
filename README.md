# hadoop HDFS cluster Installation
### Hadoop Cluster Installation
The current installation has been implemented based on :

   -	Centos 7
   -	Hadoop version 3.3.1

Installation Prerequisites

	-     Three Virtual Machines installed with Centos 7
	-     From your Ansible Control Machine(Your PC), you should have ssh key with sudo user to install the cluster
	-     Configure hostnames through your own DNS or /etc/hosts :

#### Cluster Nodes Resolution 
`  vim /etc.hosts   `

     192.168.35.X  hdfs-master.lab.example.com 
     192.168.35.X  hdfs-worker01.lab.example.com
     192.168.35.X  hdfs-worker02.lab.example.com   
```

[========]
### 1) cloning ansible files
 	- `git clone https://github.com/mshgayar/hadoop.git`
  	- `vim inventory`
 

    [datanodes]
            hdfs-worker01.lab.example.com 
            hdfs-worker02.lab.example.com
    
       [namenode]
          hdfs-master.lab.example.com
    
        [servers:children]
    datanodes
    namenode
    
    [servers:vars]
    ansible_ssh_user=YOUR-USER
    ansible_become=true
    ansible_ssh_private_key_file=~/.ssh/YOUR-KEY
    ansible_ssh_common_args='-o StrictHostKeyChecking=no'
 

##### 2)  install all the required prerequisites and packages
`ansible-playbook -i inventory hadoop.yml -t preinstall`

##### 3) perform hardening and tuning
`ansible-playbook -i inventory hadoop.yml -t hardening`

#####** before installing the ansible hadoop role  and perform the below : On Master-node**



    sudo -i
    passwd hadoop # configure user "hadoop: password
    su - hadoop
    ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    chmod 0600 ~/.ssh/authorized_keys
    ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hdfs-worker01.lab.example.com -f
    ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hdfs-worker02.lab.example.com -f
	

##### 4) Install the hadoop cluster & initialize hdfs and all services
`ansible-playbook -i inventory hadoop.yml -t hadoop`
##### 5) access the cluster : 

    From browser : http://hdfs-master.lab.example.com:9870/
    or you can use the monitoring ansilbe role
         -   ansible-playbook -i inventory hadoop.yml -t monitor
###End
