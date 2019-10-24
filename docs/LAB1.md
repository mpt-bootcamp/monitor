## LAB1 - Installing monitoring servers and agents
---
There is many software from open source and commercial tools which help you to monitor your infrastructure, platform, application, meta and last-mile monitoring. In this lab, we will install the following open source monitoring tools:

**Monitoring Servers***

* Check_MK
* Zabbix
* Elasticsearch Kibana
* APM Server
* Grafana

**Monitoring Agents***

* Check_MK Agent
* Zabbix Agent
* Elastsearch Beats
* APM Agent

We will also install a LAMP stack and a Java web application to demonstrate the platform and application monitoring.


### Exercise 1 - Installing the monitoring servers

First, Open the ***Jupyterhub*** web terminal console with this URL:

> http://console\<n\>.missionpeaktechnologies.com:8000

**Note** to replace \<n\> with your assigned student number. For example,

> http://console2.missionpeaktechnologies.com:8000

Enter student<n>/student<n> for the username and passowrd. For example, student2/student2

Once login, open the Terminal window and run the following commands to download the ***monitor*** GitHub repo: 


```console
$ mkdir -p ~/bootcamp
$ cd ~/bootcamp
$ git clone https://github.com/mpt-bootcamp/monitor.git
$ cd monitor/playbooks
```

Now you can use the Ansible playbooks provided to install the monitoring servers

```console
$ cd ~/bootcamp/monitor/playbooks
$ ansible-playbook -i localhost, -c local -kK deploy-monitor-console.yml
```

Enter the your student user passowrd when it prompts. For example,

```
SSH password: 
BECOME password[defaults to SSH password]:
```

It will take a few minutes for Ansible to run the playbook to install and configure the monitoring servers.

### Exercise 2 - Installing the monitor agents

In order for the monitoring servers to communicate and collect monitoring metrics from the hosts or devices, we also need to install the monitoring agents on those hosts.

From your **Jupyterhub** Terminal window, edit the playbook, *deploy-monitor-agents.yml*, and change the playbook variables to reflect your assigned hostname for the monitoring server. 

```console
$ pwd
$ ls -ltr *.yml
$ clear
$ vi deploy-monitor-agents.yml
```

**Replacing the *console1* hostname with your assigned hostname. For example, *console2*.

Here is the original playbook variables:

```
  vars:
    elasticsearch_url: "console1.lab.mpt.local:9200"
    kibana_url: "console1.lab.mpt.local:5601"
    checkmk_only_from: "localhost console1.lab.mpt.local"
    zabbix_host: "console1.lab.mpt.local"
```

Once the playbook variables are updated, you can run the followng commands install the agents to the *runner* host, runner\<n\>.lab.mpt.local. 

**Note** to replace \<n\> with your assigned student number. For example, runner2.lab.mpt.local


```console
$ cd ~/bootcamp/monitor/playbooks
$ ansible-playbook -i runner<n>.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-monitor-agents.yml
```

This should take a few minutes to install and configure the agents using Ansible.


### Exercise 3 - Installing the LAMP stack and a Java web application

At the Jupyterhub Terminal window, edit the paybook, deploy-assets-manager.yml, to change hostname to match your assigned student console number. For example, ***console2***. Here is the original playbook variables:

```
  vars:
    app_owner: appsvc
    app_group: appsvc
    github_user: mpt-bootcamp
    amp_server_url: "console1.lab.mpt.local:9200"
```

```console
$ pwd
$ clear
$ vi deploy-assets-manager.yml
```

Now run the playbooks to deploy the LAMP stack and Java application that will be used for the lab exercises.

```console
$ cd ~/bootcamp/monitor/playbooks
$ ansible-playbook -i runner<n>.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-lamp.yml
$ ansible-playbook -i runner<n>.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-assets-manager.yml
```

Before we start setting up monitoring, let's login to the installed monitoring servers and navigate around.

### Exercise 4 - Connecting to monitoring servers and applications

With a browser (Chrome), open the each of the following URLs and login with the default admin credential.

| Monitor  |      URL      |  Username/Password |
|----------|-------------|------|
| Check_MK | http://console\<n\>.missionpeaktechnologies.com/lab | cmkadmin/cmkadmin |
| Zabbix | http://console\<n\>.missionpeaktechnologies.com/zabbix   | Admin/zabbix |
| Kibana | http://console\<n\>.missionpeaktechnologies.com:5601 |     |
| Grafana | http://console\<n\>.missionpeaktechnologies.com:3000 | admin/admin  |
| LAMP | http://runner\<n\>.missionpeaktechnologies.com |   |
| Assets Manager | http://runner\<n\>.missionpeaktechnologies.com:9000 |   |

Next, let's create a user and a user group used for alert notification on those servers.

### Exercise 5 - Creating user and group in the monitor servers.

**Note** when creating a user account, make sure you enter a valid email address, so that you can send email notification


#### Check_MK

Check_MK has the concept of Contacts, Contact Groups and Roles. The default three roles are:

* **Admins** - have complete administrative control
* **Users** - have some control over the objects they are assigned
* **Guests** - users are usually limited only viewing data


**Creating a user group**

1. Login to Check_MK with the admin account, cmkadmin.
2. Select ***WATO->Contact Groups->New contact group***
3. Then enter the following information:

> Name: oncall
> Alias: OnCall
> **PERMISSIONS**
> Permitted HW/SW inventory paths: Allowed to see the whole tree

Click "Save". Then the "Change" and "Activate Affected" button to apply and activate the changes.

**Creating a user**

1. Select ***WATO->Users->New use*** to create a student user. 
2. Enter the following information (**Note** to replace \<n\> with your student number and provide your personal email address):

> **IDENTITY**
> Username: student\<n\>
> Full name: student\<n\>
> Email address: \<Your valid Yahoo or Gmail address\>
> password: student\<n\>
> repeat: student\<n\> 
> **SECURITY**
> [x] Normal monitoring user
> **CONTACT GROUPS**
> [x] OnCall

Click the "Save" button when "Change" and Activate Affected" activate the change. 

#### Zabbix

For Zabbix, the default roles are:

* **User** - The user has access to the Monitoring menu. The user has no access to any resources by default. Any permissions to host groups must be explicitly assigned.
* **Admin**	- The user has access to the Monitoring and Configuration menus. The user has no access to any host groups by default. Any permissions to host groups must be explicitly given.
* **Super Admin** -	The user has access to everything: Monitoring, Configuration and Administration menus. The user has a read-write access to all host groups. Permissions cannot be revoked by denying access to specific host groups.


**Creating a user group**


1. Login to Zabbix and go to Administration->User groups
2. Click on *Create user group* (or on the group name to edit an existing group)
3. Edit group attributes in the form

> **User group** tab
> Group name: OnCall
> Frontend access: System default
> [x] Enabled
> ** Permission** tab
> Click "Select" button and pick ***Linux Server***, then ***Read*** permision. Remember to click the "Add" link (not the button)
> Click the **Add** button to add the user group. 

Then click the "Add" button to create the user group. 

**Creating a Zabbix user account**

1. Login to Zabbix and go to Administration->User
2. Click *Create user**. Enter the following information.

**User** tab
> Alias: student\<n\>
> Name: student\<n\>
> Groups: OnCall
> Password: student\<n\>
> Password (once again): student\<n\>
> Language: English (en_US)
>
> **Media** tab 
> Click the "Add" link in the Media box to open the dialog box. 
> Type: Email
> Send to: \<Your valid Yahoo or Gmail address\>
>

Click the "Add" button to save.



### Exercise 6 - Login to your student account

To verify your student account created, logout and log back in to Zabbix and Check_MK with your student account created.

| Monitor  |      URL      |  Username/Password |
|----------|-------------|------|
| Check_MK | http://console\<n\>.missionpeaktechnologies.com/lab | cmkadmin/cmkadmin |
| Zabbix | http://console\<n\>.missionpeaktechnologies.com/zabbix   | Admin/zabbix |

For the other monitoring servers, we will use the default administrator account for this bootcamp. 

---
## End









