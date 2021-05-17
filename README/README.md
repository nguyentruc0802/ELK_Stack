## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

(Images/PJ1Draper.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the filebeat-playbook.yml file may be used to install only certain pieces of it, such as Filebeat or Metricbeat.

---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

  - name: drop in filebeat.yml
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start

  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes
      state: started

  - name: download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb

  - name: install metricbeat
    command: dpkg -i metricbeat-7.6.1-amd64.deb

  - name: drop in metricbeat.yml
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

  - name: enable and configure docker module
    command: metricbeat modules enable docker

  - name: set up metricbeat
    command: metricbeat setup -e

  - name: start metricbeat
    command: service metricbeat start

  - name: enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: yes
      state: started

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting DDOS attacks to the network.
- Load balancers ensure the Availability piece of the CIA triad. The advantage of a jump box? The jump box serves as a gateway and avoids having to expose every machine directly to the public internet.  Strong access controls can then be implemented on a single machine rather than on every individual VM, drastically reducing the attack surface.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- What does Filebeat watch for?
  Data about the file system.  Specifically, it logs information about the file system, including which files have been changed and when.
- What does Metricbeat record?
  Machine metrics, such as uptime.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) 

| Name           | Function  | IP Address | Operating System |
|----------------|-----------|------------|------------------|
|                |           |            |                  |
| JumpBox-OffSec | Gateway   | 10.1.0.4   | Linux            |
| Web1-OffSec    | Container | 10.1.0.5   | Linux            |
| Web2-OffSec    | Container | 10.1.0.6   | Linux            |
| Web3-OffSec    | Container | 10.1.0.7   | Linux            |
| ELK-VM         | Monitor   | 10.0.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the host machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 73.43.166.201

Machines within the network can only be accessed by SSH and HTTP via JumpBox and ELK VM.
- The ELK is accessible by the Client 73.43.166.201 and JumpBox 10.1.0.4

A summary of the access policies in place can be found in the table below.

| Name           | Publicly Accessible | Allowed IP Address      |
|----------------|---------------------|-------------------------|
|                |                     |                         |
| JumpBox-OffSec | yes                 | 73.43.166.201,          |
| Web1-OffSec    | no                  | 10.1.0.4, 10.0.0.4      |
| Web2-OffSec    | no                  | 10.1.0.4, 10.0.0.4      |
| Web3-OffSec    | no                  | 10.1.0.4, 10.0.0.4      |
| ELK-VM         | yes                 | 73.43.166.201, 10.1.0.4 |
| OffSec-LB      | yes                 | 73.43.166.201           |


               

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- CI and CD. Continuous Intergration and Continous Deployment.

The install-elk.yml playbook implements the following tasks:
- Installs Docker
- Installs Python version 3 if not present
- Downloads and Launches Docker ELK container
 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

(Images/PROJECT 1-PART 4 SCREENSHOT.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.1.0.5
  10.1.0.6
  10.1.0.7

We have installed the following Beats on these machines:
- Filebeats
  Metricbeats

These Beats allow us to collect the following information from each machine:
- Filebeat allows us to collect log data from each machine.  We can view sys logs, var logs, and be able monitor any changes that take place is the logs.
  Metricbeat monitors system metrics.  Things like uptime and memory usage can be expected to be found here.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the __filebeat-playbook.yml___ file to __/etc/ansible/roles___.
- Update the __/etc/ansible/hosts___ file to include [webservers] and their correlating IP addresses and [elk] and it's correlation IP address
- Run the playbook, and navigate to __http://publicIPaddress:5601/app/kibana__ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._