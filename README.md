# ELK-STACK-PROJECT
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
![Network Diagram](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/NET-DIAGRAM.jpg)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yml and config files may be used to install only certain pieces of it, such as Filebeat.

 * [My First Playbook](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/pentest.yml "My First Playbook")
* [Hosts](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/hosts.yml)
* [Ansible Configuration](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/ansible.cfg "Ansible Configuration File")
* [Ansible ELK Installation and VM Configuration](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/install-elk.yml "ELK Installation and VM Configuration file")
* [Filebeat Config](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/filebeat-configuration.yml "Filebeat Configuration File")
* [Filebeat Playbook](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/filebeat-playbook.yml "Filebeat Playbook")
* [Metricbeat Config](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/metricbeat-configuration.yml "Metricbeat Configuration File")
* [Metricbeat Playbook](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/metricbeat-playbook.yml "Metricbeat Playbook")

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
## Description of the Topology  

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.
Load balancing ensures that the application will be highly available, functional, in addition to restricting access to the network.

- What aspect of security do load balancers protect?
  - The Load balancers add resiliency by rerouting live traffic from one server to another if a server falls prey to a DDoS attack or otherwise becomes unavailable. It is shifting attack traffic from corporate server to a public cloud provider. 

- What is the advantage of a jump box?
  -  A jump box is a secure computer that all admins first connect to before launching any adminitrative task or use as an orgination point to connect to toher servers or untructed environments. A Jump Box Provisioner is also important as it prevents Azure VMs from being exposed via a public IP Address. This allows us to do monitoring and logging on a single box. We can also restrict the IP addresses able to communicate with the Jump Box, as we've done here.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system logs.

- What does Filebeat watch for?
  - Filebeat is a lightweight shipper for forwarding and centralizing log data. Installed as a agent on servers. Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
 
- What does Metricbeat record?
  - Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash. It helps to monitor your servers and by collecting metrics from the system and services running on the server, such as Apache. 

The configuration details of each machine may be found below.

| Name              | Function        | IP Address               | Operating System   |
|-------------------|-----------------|--------------------------|--------------------|
| Jump Box          | Gateway         | 10.0.0.4 / 52.170.69.148 | Linux              |
| Web-1             | UbuntuServer    | 10.0.0.7 / 40.114.124.38 | Linux              |
| Web-2             | UbuntuServer    | 10.0.0.8 / 40.114.213.38 | Linux              |
| ELKserver         | UbuntuServer    | 10.1.0.4 / 20.93.136.243 | Linux              |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Server machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 173.77.201.241 through TCP PORT 5601.
Machines within the network can only be accessed by JUMP BOX VM and workstation through jump box ssh.
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address? Jump Box VM IP: 10.0.0.4 via ssh port 22. 

A summary of the access policies in place can be found in the table below.
|        Name        |  Publicly Accessible  |          Allowed IP Addresses           |
|--------------------|-----------------------|-----------------------------------------|
| Jump Box           | Yes                   | 173.77.201.241(Workstation IP on SSH 22)|
| Web-1              | No                    | 10.0.0.7 on SSH 22                      |
| Web-2              | No                    | 10.0.0.8 on SSH 22                      |
| ELK server         | No                    | 10.1.0.4 on SSH 22                      |
|           

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- What is the main advantage of automating configuration with Ansible?_
Ansible is fast and performs all functions over ssh and dosent require agent installation. The main advantage is Ansible can be run from the command line without the use of configuration files for  simple taks, such as making a service is running, or to trigger updates or reboots

The playbook implements the following tasks:
-  In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
Specify a different group of machines:
      ```yaml
        - name: Config elk VM with Docker
          hosts: elk
          become: true
          tasks:
      ```
  - Install Docker.io
      ```yaml
        - name: Install docker.io
          apt:
            update_cache: yes
            force_apt_get: yes
            name: docker.io
            state: present
      ``` 
  - Install Python-pip
      ```yaml
        - name: Install python3-pip
          apt:
            force_apt_get: yes
            name: python3-pip
            state: present

          # Use pip module (It will default to pip3)
        - name: Install Docker module
          pip:
            name: docker
            state: present
            `docker`, which is the Docker Python pip module.
      ``` 
  - Increase Virtual Memory
      ```yaml
       - name: Use more memory
         sysctl:
           name: vm.max_map_count
           value: '262144'
           state: present
           reload: yes
      ```
  - Download and Launch ELK Docker Container (image sebp/elk)
      ```yaml
       - name: Download and launch a docker elk container
         docker_container:
           name: elk
           image: sebp/elk:761
           state: started
           restart_policy: always
      ```
  - Published ports 5044, 5601 and 9200 were made available
      ```yaml
           published_ports:
             -  5601:5601
             -  9200:9200
             -  5044:5044   
 
 ## Target Machines & Beats  
This ELK server is configured to monitor the following machines:

- List the IP addresses of the machines you are monitoring
  - Web-1: 10.0.0.7
  - Web-2: 10.0.0.8
  - ELK-SERVER: 10.1.0.4
  
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
![Docker_ps_screenshot](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/elk-docker-vm.jpg)
Filebeat, will be used to collect log files from very specific files such as Apache, Microsft Azure tools and web servers, MySQL databases.
![Filebeat Module Status Screenshot](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/filebeat-elkvm-image.jpg)

Metricbeat, is used to collect CPU stat, filesystem stat, memeory stat, number of containers.
![Metricbeat Module status Screenshots](https://github.com/iphdealor/ELK-STACK-PROJECT/blob/main/metric-beat-docker-image.jpg)






