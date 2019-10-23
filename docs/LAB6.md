## LAB6 - Implementing a last-mile monitoring
---

Monitoring infrastructure, application metrics, etc. is very standard, but monitoring user experience is a relatively new type of monitoring. You can proactively simulate user behavior to validate availability, functionality, and performance for continuous, last-mile monitoring of your application. Some of the well-known last-mile monitoring tools are 

* [UptimeRobot](https://uptimerobot.com/)
* [New Relic](https://newrelic.com/signup?trial=apm)
* [PagerDuty](https://www.pagerduty.com)
* [Uptime](https://uptime.com)
* [Pingdom](https://www.pingdom.com/sign-up/)
* [CatchPoint](https://www.catchpoint.com)
* [Apica](https://resources.apicasystems.com/trial)



### Exercise 1- Using *UptimeRobot* for last-mile monitoring

A cloud-based monitoring service can be used to cover last-mile monitoring. If that service is available, it can be used to replace meta-monitoring, provided that meta-monitoring endpoints are accessible from the Internet as REST APIs or health-check URLs.

1. Open the following URL to sign up
https://uptimerobot.com/signUp

2. Enter the following information

> First and Last Name
> E-Mail
> Password

3. Open your email to activate the account.
4. Then login to UptimeRobot to add a new monitor

> Monitor Type: HTTP(s)
> Friendly Name: Zabbix Last Mile
> URL: http://runner\<n\>.missionpeaktechnologies.com/zabbix
> [x] Alert Contact

![UptimeRobot](images/uptimerobot-last-mile-1.png)
![UptimeRobot](images/uptimerobot-last-mile-2.png)
![UptimeRobot](images/uptimerobot-last-mile-3.png)

### Conclusion

A combination of access to public cloud-based infrastructure and SaaS based monitoring services eliminate the need to build meta-monitoring explicitly. However you still need to ensure that your monitoring is monitored. It is important that your monitoring infrastructure and processes cover all the requirements of meta-monitoring to make the overall monitoring system highly reliable.