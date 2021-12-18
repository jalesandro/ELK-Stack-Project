## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Elk Diagram](https://user-images.githubusercontent.com/87720740/146467687-a405100c-f1ff-47b6-8848-b13122116c80.jpg)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the ansible playbook elk.yml file may be used to install only certain pieces of it, such as Filebeat.

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

---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

    Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.4.0-amd64.deb

    Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    Use command module
  - name: setup metric beat
    command: metricbeat setup

    Use command module
  - name: start metric beat
    command: service metricbeat start
    
### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the _____ file to _____.
- Update the _____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
