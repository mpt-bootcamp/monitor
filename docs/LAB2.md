## LAB2 - Infrastructure monitoring
---

Infrastructure monitoring becomes more complex as IT infrastructures become both denser and dispersed. Full-stack infrastructure monitoring includes:

* Hardware – Physical Health
* Operating System and Platform – Utilization and load
* Network – Bandwidth consumption and errors
* Application – Performance and availability

In this lab, we will learn how to setup linux server monitoring using Zabbix and Check_MK. 

1. Adding a server
2. Configuring and tuning checks or templates for discovery and monitoring
3. Setting up notification
4. Acknowledging and handling alerts

## Using Zabbix

### Exercise 1 - Adding a server

Login to Zabbix as the administrator.

> http://console\<n\>.missionpeaktechnologies.com/zabbix


**Select *Configuration->Hosts->Create Host*. Then fill in the information below:**

> **Host** tab
> Hostname: runner\<n\>.lab.mpt.local
> Visual name: runner\<n\>
> Group: Linux servers
> IP address: You can find out the IP address by pinging runner\<n\>.lab.mpt.local 
> DNS name: runner\<n\>.lab.mpt.local
> **Template** tab
> Link new templates: ***Template OS Linux***
> Click the "Add" link underneath

Click the **Add** buton to save.

![zabbix add host](images/zabbix-add-host-ex1a.png)
![zabbix add host](images/zabbix-add-host-ex1b.png)

Once a host is added, Zabbix will run a host discovery in the background to identify the monitoring metrics based on the selected template, *Template OS Linux*. To view the discovered metrics,


1.Select *Configuration->Hosts*. Then click on link for each column for the associated host to see the detail of the discovery.

![zabbix add host](images/zabbix-add-host-ex1c.png)


**Enabling or disabling the triggers**

Triggers are logical expressions that “evaluate” data gathered by items and represent the current system state. To enable and disable triggers, 

1. Select *Configuration->Hosts*, then the *Triggers* link to open the list of triggers
2. Click "Enable" or "Disabled" to toggler

![zabbix add host](images/zabbix-add-host-ex1d.png)

**Creating a new trigger**

1. Go to *Configuration->Hosts*
2. Click on *Triggers* on the selected row of the host
3. Click on Create trigger to the right

Enter parameters in the ***Tigger*** tab.

> Name: CPU is overloaded on {HOST.NAME}
> Serverity: Warning
> Experession: {runner\<n\>.lab.mpt.local:system.cpu.load[percpu,avg1].last()}>5
> OK event generation: Expression
> PROBLEM event generation mode: Single
> OK event closes: All problems
> Allow manual close [x]
> Enabled [x]

![custom trigger](images/hosts-trigger-cpu-overloaded-example.png)


**Configuring and enabling email notification**

1. Click on *Administration*, and then *Media types* in the top navigation bar. You will see the list of all media types. Click on *Email*.

Enter parameters in the ***Media type*** tab.

> Name: Gmail
> type: Email 
> SMTP server: smtp.gmail.com
> SMTP server port: 587
> SMTP helo: gmail.com
> SMTP email: mpt.bootcamp@gmail.com
> Connection security: STARTTLS
> Authentication: Username and password
> Username: mpt.bootcamp@gmail.com
> Password: Mpt@9876
> Enabled [x]

Click the **Update** button to save.

![add gmail](images/administration-media-type-gmail.png)


2. Next, select menu *Configuration->Actions*. Then click "Report problem to Zabbix administrators" link. 
3. On the *Operations* tab, find *Operations* line and click the *edit* link to open the Operation detail. 
4. Then add the "OnCall" group to "Send to User groups"
5. Last ensure the action "Report problem to Zabbix administrators" is enabled.

Click the *Update* link then the **Update" button to save.

![add oncall](images/configuration_actions_user_group.png)

**Testing the email notification**

A simple way to trigger a problem and send an email notification is to shutdown the zabbix agent on a monitoring host. 

1. From the Jupyterhub control console, Login to your *runner* host.

```console
$ ssh -i ~/.ssh/id_rsa_ubuntu ubuntu@runner<n>.lab.mpt.local
```

2. Once connected, then shutdown the Zabbix agent with this command

```console
$ sudo systemctl stop zabbix-agent
```

In a few minutes (5-10), you should receive an email notification.

![problem notification](images/problem-notification-email.png)


**Acknowledging an alert**

To acknowledge a problem, go to *Monitoring->Problems*. The find the problem host. On the "Ack" column, click the "No" to open the form to put in the acknowlowedge information.

![problem host](images/problem-host-un-ack.png)

> Message: Test alert
> Acknowledge [x]

![problem host](images/problem-host-ack.png)


**Handling and resolving an alert**

To fix and clear the alert we just created, we just need to start the Zabbix agent again to fix the problem.

From the runner\<n\> host, 

```console
$ sudo systemtl start zabbix-agent
```

You should receive an email notification the problem host is resolved.


## Using Check_MK

Next, we will use Check_MK to setup host monitoring.


### Exercise 2 - Adding a host

**Login to Check_MK as the administrator, and on the WATO view**,

> http://console\<n\>.missionpeaktechnologies.com/lab 

1. Select *WATO->Hosts->New Host*
2. Enter the Hostname, runner\<n\>.lab.mpt.local , then click "Save & go to Services"
3. Click the "Change" button on the top left of the page, then click "Activate the affected" button to start scanning the checks/services
4. After a few minutes, you should see a list of services discovered by Check_MK.

**Note** Ensure the Check_MK agent disabled is set to no in 

```console
sudo cat etc/xinetd.d/check_mk
``` 

Restart xinetd as needed (sudo systemctl start|restart xinetd)


![Add host 1](images/check_mk_add_host_1.png)
![Add host 2](images/check_mk_add_host_2.png)
![Add host 3](images/check_mk_add_host_3.png)
![Add host 4](images/check_mk_add_host_4.png)
![Add host 5](images/check_mk_add_host_5.png)

**Configuring Email notification**

In order to receive alert notification, we need to associate a contact group to the host.

1. Select WATO->Hosts, then click the host to open the configuration page.
2. Check the *Permissions" section to add the "OnCall" contact group (highlight and click the ">" button or doubleclick on "OnCall")
3. Click "Save and Finish"
4. Then the "Change" and "Activate affected" buttons to apply the changes.

![Add contact group 1](images/check_mk_add_contact_group_1.png)
![Add contact group 2](images/check_mk_add_contact_group_2.png)
![Add contact group 3](images/check_mk_add_contact_group_3.png)
![Add contact group 4](images/check_mk_add_contact_group_4.png)

**Configuring Slack notification**

1. Select WATO->Notification
2. Click "New Rule" to add a new notification
3. Enter the following information:

> Description: MPT Bootcamp Slack
> Overriding by users: [x]
> Notification Method: Slack or Mattermost
> Webhook-URL: https://hooks.slack.com/services/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

Click the **Save** button to save.

![Slack Notification](images/check-mk-add-slack-1.png)
![Slack Notification](images/check-mk-slack-1.png)
![Slack Notification](images/check-mk-slack-2.png)

**Changing the Number of Thread service threshold**

In this step, we will change the threshold parameters of the "Number of Threads" service

1. From WATO, select "Hosts->Services" to open the list of services.
2. Click the "View and edit the parameters for this services" icon next to the "Number of Threads" service check to open the service parameters view.

![Slack Notification](images/check-mk-services-param-1.png)

3. Click the "Number of Thread" to open the ruleset page

![Slack Notification](images/check-mk-services-param-2.png)

4. Next, click "Create Host specific rule for:" and enter the following parameters:

> Description: My Number of Thread Threshold
> VALUE: [x] Absolute level
> VALUE:     Warning at: 200
> VALUE:     Critical at: 400

5. Click Save, Change, then Activate affected apply the new rule

After a few minutes, the "Number of Threads" service should turn "Yellow" for the warning alert. You should also receive and email and Slack notification

![Slack Notification](images/check-mk-services-param-4.png)

**Acknowledging an alert**

To acknowledge an alert, 

1. Open the "Services" view for that host
2. Click on the service, "Number of Thread", that triggered the alert
3. Click the hammer icon to open service command page and enter a comment to "Acknowledge" the the alert
4. Once confirm, you should receive a notification of acknowledgement.

![Slack Notification](images/check-mk-alert-ack-1.png)
![Slack Notification](images/check-mk-alert-ack-2.png)
![Slack Notification](images/check-mk-alert-ack-3.png)

---
## END