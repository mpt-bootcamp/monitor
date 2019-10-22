## LAB6 - Implementing a last-mile monitoring
---
Some of the well-known last-mile monitoring tools are 

https://uptimerobot.com/
https://newrelic.com/signup?trial=apm
https://www.pagerduty.com
https://uptime.com/pricing
https://www.pingdom.com/sign-up/
https://www.catchpoint.com/trial
https://resources.apicasystems.com/trial


Monitoring infrastructure, application metrics, etc. is very standard, but monitoring user experience is a relatively new type of monitoring that could be specific to SaaS. There is a considerable physical distance between where the application is running and the users are based at.


### Exercise 1- Using *UptimeRobot* for lst-mile monitoring

A cloud-based monitoring service can be used to cover last-mile monitoring. If that service is available, it can be used to replace meta-monitoring, provided that meta-monitoring endpoints are accessible from the Internet as REST APIs or health-check URLs.

1. Signing up

> https://uptimerobot.com/signUp



### Conclusion

A combination of access to public cloud-based infrastructure and SaaS based monitoring services eliminate the need to build meta-monitoring explicitly. However you still need to ensure that your monitoring is monitored. It is important that your monitoring infrastructure and processes cover all the requirements of meta-monitoring to make the overall monitoring system highly reliable.