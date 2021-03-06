## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Elk Diagram](https://user-images.githubusercontent.com/87720740/151092167-38babed2-a20f-4d2c-9102-8ef6213706a4.jpg)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the ansible playbook elk.yml file may be used to install only certain pieces of it, such as Filebeat.

- _install-elk.yml_
- _filebeat-playbook.yml_

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network. The load balancer (RedTeam-LB) efficiently distributes traffic through the backend pool and ensures the work to process that traffic will be shared by both vulnerable web servers. The access controls ensure that only authorized users, ourselves, will be able to connect in the first place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network and system metrics.

The configuration details of each machine may be found below.
| Name               | Function   | IP Address | Operating System |
|--------------------|------------|------------|------------------|
| JumpBoxProvisioner | Gateway    | 10.0.0.4   | Linux            |
| Web-1              | Web Server | 10.0.0.5   | Linux            |
| Web-2              | Web Server | 10.0.0.6   | Linux            |
| ELK-SERVER         | Monitoring | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBoxProvisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
75.108.24.100

Machines within the network can only be accessed by each other. The Web-1 and Web-2 VMs send traffic to the ELK-SERVER.

A summary of the access policies in place can be found in the table below.

| Name               | Publicly Accessible | Allowed IP Address |
|--------------------|---------------------|--------------------|
| JumpBoxProvisioner | Yes                 | 75.108.24.100      |
| ELK-SERVER         | No                  | 10.1.0.4           |
| Web-1              | No                  | 10.0.0.5           |
| Web-2              | No                  | 10.0.0.6           |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it saves us time and it helps with the representation of Infrastructure as Code.

The playbook implements the following tasks:
- Installs Docker and Pip3, the package manager for Python 3. 
- Increases the memory usage to be able to run the ELK container, specifically Kibana.
- Downloads and launches the ELK container.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
![Docker_PS](https://user-images.githubusercontent.com/87720740/146648288-864ba995-6232-4eb7-921f-487c616972a8.jpg)

The playbook is duplicated below.

```
---
# install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
```
    
### Target Machines & Beats
This ELK server is configured to monitor the following machines: Web-1 and Web-2 at 10.0.0.5 and 10.0.0.6, respectively.

We have installed FileBeat on this machine.

This Beat allows us to detect changes to the filesystem and collect Apache logs.

The playbook below installs FileBeat on the target hosts.
```
---
- name: Installing and launch filebeat
  hosts: webservers
  become: yes
  tasks:

    # Use command module
 
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

    # Use command module 

  - name: Install filebeat
    command: dpkg -i filebeat-7.6.1-amd64.deb

    # Use copy module

  - name: Drop in filebeat.yml
    copy: 
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: Enable and configure the system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebbeat service
    command: service filebeat start

  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes
```

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbooks to the Ansible Control Node.
- Run the playbooks
- Navigate to http://[IP Address]:5601/app/kibana 
