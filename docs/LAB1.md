## LAB1 - Installing monitors and agents
---

In this lab, we will install the following monitors and agents

### Monitor Servers

* Check_MK
* Zabbix
* Elasticsearch Kibana
* APM Server
* Grafana

### Monitor Agents

* Check_MK Agent
* Zabbix Agent
* Elastsearch Beats
* APM Agent

We will also install a LAMP stack and Java web application to demonstrate the platform and application monitoring.


### Exercise 1 - Installing the monitor servers

At the **Jupyterhub** Terminal window, run the follow commands and Ansible playbook to install the monitor servers.

```console
mkdir -p ~/bootcamp
cd ~/bootcamp
git clone https://github.com/mpt-bootcamp/monitor.git
cd monitor/playbooks

student1@console1:~/bootcamp/monitor/playbooks$ ansible-playbook -i localhost, -c local -kK
```

Enter the your student user passowrd when it prompts. For example,

```
SSH password: 
BECOME password[defaults to SSH password]:
```

### Exercise 2 - Installing the monitor agents

Once the monitor servers are installed on the console server, we can proceed to install the agents on the runner machine. Before you start running the installation steps, you need to update the Ansible playbook to tell the agents to the monitor servers to connect to and to report the monitoring metrics.

At the same **Jupyterhub** Terminal window, edit the paybook, *deploy-monitor-agents.yml*, and change the ***console1*** hostname name to match your assigned student console number. For example, ***console2***.

```
  vars:
    elasticsearch_url: "console1.lab.mpt.local:9200"
    kibana_url: "console1.lab.mpt.local:5601"
    checkmk_only_from: "localhost console1.lab.mpt.local"
    zabbix_host: "console1.lab.mpt.local"
```

Once the playbook variables are updated, you can run the followng commands install the agents.

```console
cd ~/bootcamp/monitor/playbooks

ansible-playbook -i runner1.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-monitor-agents.yml
```

### Exercise 3 - Installing the LAMP stack and Java web application

At the same Jupyterhub Terminal window, edit the paybook, deploy-assets-manager.yml, and change the console1 hostname name to match your assigned student console number. For example, ***console2***.

```
  vars:
    app_owner: appsvc
    app_group: appsvc
    github_user: mpt-bootcamp
    amp_server_url: "console1.lab.mpt.local:9200"
```

Now run the playbooks.

```console
cd ~/bootcamp/monitor/playbooks

ansible-playbook -i runner1.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-lamp.yml

ansible-playbook -i runner1.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-assets-manager.yml
```

### Exercise 4 - Connecting to monitor servers and applications

From the personal laptop computer open and login to each of the following URLs with the default administrator username and password.

| Monitor  |      URL      |  Username/Password |
|----------|-------------|------|
| Check_MK | http://console\<n\>.missionpeaktechnologies.com/lab | cmkadmin/cmkadmin |
| Zabbix | http://console\<n\>.missionpeaktechnologies.com/zabbix   | Admin/zabbix |
| Kibana | http://console\<n\>.missionpeaktechnologies.com:5601 |     |
| Grafana | http://console\<n\>.missionpeaktechnologies.com:3000 | admin/admin  |
| LAMP | http://runner\<n\>.missionpeaktechnologies.com |   |
| Assets Manager | http://runner\<n\>.missionpeaktechnologies.com:9000 |   |


### Exercise 5 - Creating a student user account in the monitor servers.

When creating the user account, make sure you enter a valid email address, so that you can setup and receive email alert notification.

**Creating a Check_MK user account**

Login to Check_MK with the admin account, 



