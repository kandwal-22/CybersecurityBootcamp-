# CybersecurityBootcamp-
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![](https://github.com/kandwal-22/CybersecurityBootcamp-/blob/main/Diagrams/Project-diag.PNG)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the **yml and config** file may be used to install only certain pieces of it, such as Filebeat.

  - [Hosts](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/hosts "Hosts File")
* [Ansible Configuration](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/ansible.cfg "Ansible Configuration File")
* [Ansible ELK Installation and VM Configuration](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/install-elk.yml "ELK Installation and VM Configuration file")
* [Filebeat Config](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/filebeat_config.yml "Filebeat Configuration File")
* [Filebeat Playbook](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/filebeat_playbook.yml "Filebeat Playbook")
* [Metricbeat Config](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/metricbeat-config.yml "Metricbeat Configuration File")
* [Metricbeat Playbook](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/metricbeat-playbook.yml "Metricbeat Playbook")

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly Functional and available, in addition to restricting traffic to the network.
- What aspect of security do load balancers protect? What is the advantage of a jump box?-

   _Load balancers add resiliency by rerouting live traffic from one server to another if a server falls prey to a DDoS attack or otherwise becomes unavailable._
  
   _A Jump Box Provisioner is also important as it prevents Azure VMs from being exposed via a public IP Address. This allows us to do monitoring and logging on a single box. We can also restrict the IP addresses able to communicate with the Jump Box, as we've done here._

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system logs.
- What does Filebeat watch for?-

  _Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing._
- What does Metricbeat record?-

   _Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash._
   
The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    | Server   | 10.0.0.5   | Linux            |
| Web-2    | Server   | 10.0.0.6   | Linux            |
|ELK-server| Monitor  | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Red-Team Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _Workstation My Public IP through TCP 5601_

Machines within the network can only be accessed by workstation and Red-Team-Jumpbox through SSH.
- _Which machine did you allow to access your ELK VM? What was its IP address?_
  
  Red-Team-Jumpbox IP: 10.0.0.4 via SSH port 22.
  Workstation MY Public IP via port TCP 5601
  

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 20.127.65.176        |
| Web-1    | No                  | 10.0.0.5             |
| Web-2    | No                  | 10.0.0.6             |
|ELK-Server| No                  | 10.1.0.4             |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _ What is the main advantage of automating configuration with Ansible?_
    There are multiple advantages, Ansible lets you quickly and easily deploy multitier applications throug a YAML playbook.
    
     - _You don't need to write custom code to automate your systems._
     - _Ansible will also figure out how to get your systems to the state you want them to be in. 

The playbook implements the following tasks:

- - Specify a different group of machines:
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
      ```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _Web-1: 10.0.0.5_
- _Web-2: 10.0.0.6_

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the yml file to ansible folder.
- Update the config file to include remote users and ports.
- Run the playbook, and navigate to Kibana to check that the installation worked as expected.


### **_For ELK VM Configuration:_**
- Copy the [ELK Installation and VM Configuration ](https://github.com/kandwal-22/cybersecurityBootcamp/Ansible/install-elk.yml) 
- Run the playbook using this command :  `ansible-playbook /etc/ansible/install-elk.yml` 


### **_For Filebeat_**
- Download Filebeat playbook usng this command:
- 



_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
