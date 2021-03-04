### ELKthis Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![RedSec Network Topology](Project1/P1/Images/my_azure2.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Yaml file may be used to install only certain pieces of it, such as Filebeat.

  - Project1/P1/Yaml_scripts/first-play.yml
  - Project1/P1/Yaml_scripts/elk_install.yml
  - Project1/P1/Yaml_scripts/filebeat-playbook.yml
  - Project1/P1/Yaml_scripts/metricbeat-playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
- Beats in Use
- Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly resilient, in addition to restricting traffic to the network.

Load balancers help protect against DDoS attacks as well as help limit the ammount of traffic flowing inbound. An advantage to using a Jump Box is that it acts as a sort of gateway router, forcing all traffic through a single node which is called "Fanning In".This decreases the attack surface vector and also helps with securing and monitoring when implementing routing logic and designing networks since it's really the only system exposed to the public net.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the containers and system files.
- Filebeat collects log data about file systems and various changes being made within the system files
- Metricbeat compiles system metrics and outputs them to other software that help visualize the data collected

The configuration details of each machine may be found below.

| Name      | Function | Public IP Address | Local IP Address | Operating System |
|-----------|----------|-------------------|------------------|------------------|
| Jump Box  | Gateway   |   104.42.77.225   |    10.0.0.4      |  18.04.1-Ubuntu  |
| Web-1     | DVWAHost  |    104.40.5.4     |    10.0.0.11     |  18.04.1-Ubuntu  |
| Web-2     | DVWAHost  |    104.40.5.4     |    10.0.0.12     |  18.04.1-Ubuntu  |
| Web-3     | DVWAHost  |    104.40.5.4     |    10.0.0.10     |  18.04.1-Ubuntu  |
| Elk-Head  | ELKHost   |    104.40.5.4     |    10.1.0.5      |  18.04.1-Ubuntu  | 

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump-Box Provisioner can accept connections from the Internet. Access to this machine is only allowed from the following IP address:
- Local Workstation: 73.232.8.224
This connection is secured using my local workstation's public ssh key

Machines within the network can only be accessed by the ansible container located on the jump box.
- Access to the ELK-Head VM and Web1-3 VM's was given to my Jump-Box at IP: 10.0.0.4 and is secured with the ansible container's public ssh key

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump-Box |        Yes          |     73.232.8.224     |
| Elk-Head |         No          |      10.0.0.4        |
| Web-1    |         No          |      10.0.0.11       |
| Web-2    |         No          |      10.0.0.12       |
| Web-3    |         No          |      10.0.0.10       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because using a provisioner like Ansible gives us the capability to implement various sytem changes on thousands of machines from a single script. This helps with redundancy by having the configurations run indentically across systems, possibly eliminating as many differences between configurations as possible. 

The playbook implements the following tasks:
- Use apt module to install docker.io 
- Use apt module to install python3-pip
- Use pip module to install docker python library module
- Run "$ sysctl -w vm.max_map_count=262144" in a shell to increase virtual memory
- Use sysctl module to then increase memory usage
- Use the Docker_container module to download and launch the "sebp/elk:761" ELK container and ensure it always starts when the system starts up  and publish ports; 5601, 9200, 5044. 
- Use systemd module to ensure docker.io service is always running on boot up

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Proof of Successfull Deployment](\Project1\P1\Images\elkcontainer.png)

## Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1: 10.0.0.11
- Web-2: 10.0.0.12
- Web-3: 10.0.0.10

## We have installed the following Beats modules on these machines:
- Elasticsearch
- Haproxy
- Kafka
- Kibana
- Nats
- Osquery
- Santa

## These Beats allow us to collect the following information from each machine:

- Elasticsearch collects the various log files compiled, analyzes that data, then creates indexes that allow for real-time searching
- The HAProxy module logs the data collected from the load balancer HAProxy application. 
- Kafka collects log data from various events and places them into kafka topics for event streaming
- Kibana takes the structured data from elastic search and creates visualizations into a dashboard for real-time management and monitoring of data
- The NATS module logs various messages like client connection events, authentication errors, etc.
- Osquery collects and decodes the osqueryd file system result logs into the JSON format
- The Santa module parses logs from the Santa security tool which watches for process execution at a binary level

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the elk_install.yml file to /etc/ansible/.
- Update the /etc/ansible/hosts file to reflect your new group and include the IP addr. of the ELK VM
- Update the /etc/ansible/ansible.cfg file to reflect the desired login name and IP addr. of the ELK VM
- Run the playbook, and navigate to http://[ELK-PUBLIC-IP]:5601/app/kibana#

## A breakdown of the cmd's to run:
- $ ssh account@PublicIPofJumpBox 
    - Accept the key
- $ cd /etc/ansible
- $ nano /etc/ansible/hosts #update to reflect new group and IP addr. of ELK tht VM
- $ nano /etc/ansible/ansible.cfg #update to reflect login name and IP addr. of the ELK VM
- $ nano elk_install.yml # this will create a new text file for your YAML code
    - Paste text from elk_install.yml
    - Or just scp the file from your local machine, into your Ansible Container on the Jump Box
- $ sudo ansible-playbook elk_install.yml
- Sip some nice Jasmine Tea and Enjoy!