## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![TODO: Update the path with the name of your diagram](Images/diagram_filename.png)


Afetr setting up the Cloud Network using Azure portal, This project was design to deploy virtual machines onto the network to host the ELK stack server in order to configure and monitor the cloud network.
These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

  Elk.yml
  Filebeat-playbook.yml
  Metricbeat-playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting to the network.
- The load balancers can distribute incoming network traffic across multiple VMs or servers and add an additional layer of security against DoS attacks.protect?
The goal of using a JumpBox VM between the web and Elk VMs on the network is to force all taffic through a single node. It's the same concept of using a gateway router, which is beneficial as it makes it easier to implement routing logic and design networks. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
- Filebeat collects data about the fike system.
- Metricbeat collects machine metrics.

The configuration details of each machine may be found below.


| Name     | Function  | IP Address | Operating System |
|----------|---------- |------------|------------------|
| Jump Box | Gateway   | 10.0.0.4   | Linux            |
| Web-1    | Web M     | 10.0.0.6   | Linux            |
| Web-2    | Web M     | 10.0.0.7   | Linux            |
| Web-3    | Web M     | 10.0.0.8   | Linux            |
|Elk VM    | Monitoring| 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- The local workstation's public IP address

Machines within the network can only be accessed by the Jump Box machine.
- The Elk VM is accessible through SSH KEY from the Jump Box and via web access from the local workstation's public IP address

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | No                  | Public IP            |
| Web-1    | Yes (via Load Bal)  | 20.96.130.213        |
| Web-2    | Yes                 | 20.96.130.213        |
| Web-3    | Yes                 | 20.96.130.213        |
| Elk VM   | No                  | Public IP            |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
-  The Ansible is a tool that automates complex, multi IT application envirenments

The playbook implements the following tasks:

Specify a different group of machines as well as a different remote user(in case you did not use the same admin name):
---
-name: Config elk VM with Docker
-hosts:elkservers
-remote_user: elk (#if you used the same admin name you do not need this step)
-become: true
-tasks:

Increase System Memory:
   -name: Use more memory
    sysctl:
      name: vm.max_map_count
      value: '262144'
      state: present
      reload: yes

Install docker.io, python3-pip, docker (which is the Docker Python pip module):
    - name: docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

    - name: Install pip3
      apt:
        name: python3-pip
        state: present

    - name: Install Docker python module
      pip:
        name: docker
        state: present

Download and Launch the ELK container with these published ports:
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


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.



### Target Machines & Beats
This ELK server is configured to monitor the following machines:
* Web-1
* Web-2
* Web-3

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log files from specific files such as those generated by apache, Microsoft azure, Mysql. For example Winlogbeat collects Windows event logs.
- Metricbeat collects data from Operating Systems and services that run on the server. For example Metricbeat can be used to monitor and analyze CPU and memory.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-configuration.yml file to your ansible container.
- Update the filebeat-configuration.yml file to include:

Line #1106 is where you would edit the file by replacing the IP address with the IP address of your ELK machine
output.elasticsearch:
hosts: ["10.1.0.4:9200"]
username: "elastic"
password: "changeme"

Line #1806 is where you would edit the file by replacing the IP address with the IP address of your ELK machine

setup.kibana:
host: "10.1.0.4:5601"

Create a Filebeat playbook to install and launch Filebeat on the Web VMs with DVWA.

* When creating ansible playbooks its important to make sure the begging of the playbook is accurate as that is what indicates where and how the tasks will be executed. For Filebeat and Metricbeat playbooks, you want to makesure that you are having the beats installed on hosts: websevers which is how the playbook will know to install the beats on the websevers we want them to be on.
* The Filebeat playbook should look like this: filebeat-playbook.yml

Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI to check that the installation worked as expected.

* The ELK server GUI is accessible by navigating to http://[your.VM.IP]:5601/app/kibana. You replace [your.VM.IP] with the Public IP address of the ELK server that we created. If it is not working then the ELK server was created incorrectly and or configured incorrectly
* To verify this, scroll down to Step 5: Module Status on the ELK server GUI and click Check Data. If you receive the below image, than the ELK stack was successfully receiving logs:

