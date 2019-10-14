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


### Exercise 5 - Creating user and group in the monitor servers.

When creating the user account, make sure you enter a valid email address, so that you can setup and receive email alert notification.

#### Check_MK

Check_MK has the concept of Contacts, Contact Groups and Roles. 

The 3 default roles are:

* Admins - have complete administrative control
* Users - have some control over the objects they are assigned
* Guests - users are usually limited only viewing data

Contacts in a group are usually people you want to notify (email, sms, etc) when something happens within your infrastructure.

**Creating a user group**

Login to Check_MK with the admin account, cmkadmin. Then select ***WATO->Contact Groups->New contact group***, then enter the following information:

> Name: oncall
> Alias: OnCall
> Permitted HW/SW inventory paths: Allowed to see the whole tree

Click "Save". You much also click the " 1 change" then "Activate Affected" button highlight in orange color to apply and activate the change.

**Creating a user**

Then select ***WATO->Users->New use*** to create a student user. Enter the following information (**Note** to replace \<n\> with your student number and provide your personal email address):

> Username: student\<n\>
> Full name: student\<n\>
> Email address: \<Your valid Yahoo or Gmail address\>
> password: student\<n\> 
> [x] Normal monitoring user
> [x] OnCall

Click the "Save" button when done. 

####Zabbix

There are 3 default roles:

* User - The user has access to the Monitoring menu. The user has no access to any resources by default. Any permissions to host groups must be explicitly assigned.
* Admin	- The user has access to the Monitoring and Configuration menus. The user has no access to any host groups by default. Any permissions to host groups must be explicitly given.
* Super Admin -	The user has access to everything: Monitoring, Configuration and Administration menus. The user has a read-write access to all host groups. Permissions cannot be revoked by denying access to specific host groups.

**Creating a Zabbix user group**

1. Login to Zabbix and go to Administration → User groups
2. Click on Create user group (or on the group name to edit an existing group)
3. Edit group attributes in the form

> Group name: OnCall
> Frontend access: System default
> [x] Enabled

Then click the "Add" button to create the user group. 

**Creating a Zabbix user account**

Select ***Admistration->Users->Create user***. Enter the following information.

User (tab)
> Alias: student\<n\>
> Name: student\<n\>
> Groups: OnCall
> Password: student\<n\>
> Password (once again): student\<n\>
> Language: English (en_US)

Media (tab). Click the "Add" link to open the dialog box. Then enter:

> Type: Email
> Send to: \<Your valid Yahoo or Gmail address\>

Click the "Add" button to save.

#### Grafana

Grafana users have permissions that are determined by their:

* Role - Admin, Editor, Viewer
* Team/Group memberships where the Team has been assigned specific permissions.
* Prmissions assigned directly to user

**Creating a Grafana user account**

Login to Grafana as the administrator (admin). On the left menu pane, select *Configuration->Users*, then click the "Invite" button and enter:

> Email or Username: \<Your valid Yahoo or Gmail address\>
> Name: student\<n\>
> Role: Editor
> [x] Send Invite email

When finish, click the "Invite" button. You should receive an email to activate the account.

**Creating a Grafana team group**

Select *Configuration->Teams->New team*. Enter the team name and leave the email blank.

> Name: OnCall
> Email: 

**Adding member to the OnCall team**

Select *Configuration->Teams" and click the "OnCall" team. Under the Members tab, click "Add member". then select the 'student\<n\>' email from the drowdown list. Click "Add to team"


### Exercise 6 - Granting permission to a user and group

After adding the user, you need to grant permission to the user or the group in order view devices and response to events associated with them. 

#### Check_MK

In Checkmk a user is one with access to the user interface. They have one or more Roles. Once a user is made responsible for specific hosts and services, they are identified as a Contact. A contact normally sees only their own hosts and services in the user interface, and receives notifications regarding possible problems.

For Check_MK, permissions granted when you add a device and assign a contact group. We will see how it is done in **LAB2**


#### Zabbix

For Zabbix, permissions need to explicitly grant to a user group.

* Go to *Administration->User groups*
* Select the "OnCall" group, then click the "Permission" to tab to permisions.
* Click the "Select" button and check the "Linux * servers". T
* Click "Add" then select "Read" next to the the "Linux servers"
* Click the "Update" button to save


### Exercise 7 - Login to your student account

With the student account added to each of the monitoring servers, you can now login to the servers with the student account and navigate around. The purpose of creating the user account is to setup email notification in the latest labs.

Since we have yet add any devices yet, you should not see any devices listed when you login. 












