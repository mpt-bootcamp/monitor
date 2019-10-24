## LAB5 - Monitoring the monitors
---

Implementing a highly reliable method to check on the availability of monitoring systems is the key to the success of a meta-monitoring system. During the data center era, the options to implement reliable meta-monitoring were limited. But, with the availability of cloud-based infrastructure monitoring applications that task and options have become much simpler.

### Exercise 1 - Implementing a meta-monitoring

In this exercise, we will show you how to use Elastic **Heartbeat** agent to monitor both Check_MK and Zabbix.

1. From the Jupyterhub Terminal window, login to your *runner* host to configure the Heartbeat monitoring.

```console
$ssh -i ~/.ssh/id_rsa_ubuntu ubuntu@runner<n>.lab.mpt.local
```

2. Verifying Heartbeat is running

```console
$ sudo systemctl status heartbeat-elastic
```

It should return **Active and running**

```
heartbeat-elastic.service - Ping remote services for availability and log results to Elasticsearch or send to Logstash.
   Loaded: loaded (/lib/systemd/system/heartbeat-elastic.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2019-10-20 19:28:00 UTC; 8h ago
```

3. Configuring Heartbeat 

Create the following heartbeat check file with the contents below. Make sure the replace console\<n\> with our console hostname. For example console2, console3, etc

```console
$ sudo vi /etc/heartbeat/monitors.d/check_mk.yml
```
```
# /etc/heartbeat/monitors.d/check_mk.yml
- type: http
  name: check_mk
  schedule: '@every 5s'
  urls: ["http://console<n>.missionpeaktechnologies.com/lab"]
  check.response.status: 200
```

```console
$ sudo vi /etc/heartbeat/monitors.d/zabbix.yml
```

```
# /etc/heartbeat/monitors.d/zabbix.yml
- type: http
  name: zabbix
  schedule: '@every 5s'
  urls: ["http://console<n>.missionpeaktechnologies.com/zabbix"]
  check.response.status: 200
```

4. Restarting **Heartbeat** agent

```console
$ sudo systemctl restart heartbeat-elastic
$ sudo systemctl status heartbeat-elastic
```

5. Using Elastic Uptime

By using the new Elastic Uptime solution, they can now detect if the monitor (Check_MK or Zabbix) is down. 

![Kibana Uptime](images/kibana-uptime-ex1a.png)


Exercise 2 - Simulating a monitor is down

From the Jupyterhub Terminal console, stop the Check_MK server

```console
$ sudo su -l
$ omd stop lab
```

Now check the Kibana Uptime dashboard. You should see the Check_MK server is down.

![Kibana Uptime](images/kibana-uptime-ex2a.png)

Now start Check_MK again

```console
$ sudo su -l
$ omd start lab
```

Similarly, you can stop and start apache server to simulate Zabbix being down.

```console
$ sudo systemctl stop|start apache2
``` 

---
## End
