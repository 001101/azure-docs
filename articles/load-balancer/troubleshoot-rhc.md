# Troubleshoot resource health, frontend and backend availability issues 

This article is a guide to leverage the metrics and resource health check available with the Standard Load Balancer to detect and investigate issues impacting the availability of your load balancer frontend IP and backend resources. 

## About the metrics we will use
The two metrics to be used are *Data path availability* and *Health probe status* and it is important to understand their meaning to derive correct insights. 

### Data path availability
The data path availability metric is generated from an internal service which executes a TCP ping every 25 seconds on all frontend ports which have load balancing and inbound NAT rules configured. This TCP ping will then be routed to any of the healthy (probed up) backend instances, if it succeeds the Sum of the metric will be iterated once and if it fails it will not. With Count per 100 serving as the denominator of total TCP pings per sample period, the aggregation we want to consider is Average which will show the average of Sum/Count for the time period. This gives us a percentage success rate for TCP pings on your frontend IP:port for each of your load balancing and inbound NAT rules.

### Health probe status
The health probe status metric is generated from an internal service which executes a ping with the protocol defined in the health probe. This ping is sent to each member of the backend pool and on the backend port defined in the health probe. For HTTP and HTTPS probe a succesful ping requires an HTTP 200 OK response. This then determines whether a backend pool member is healthy and able to receive traffic for the load balancing rules of which the backend pool is assigned. Similar to data path availability we use the Average aggregation which tells us the average succesful/total pings during the sampling interval. This indicates the backend health by checking your backend instances completely seperate from any traffic being sent through the frontend.

## Using data path availability and health probe status to diagnose degraded and unavailable load balancers
As outlined in the [resource health article](load-balancer-standard-diagnostics.md#resource-health-status), a degraded load balancer is one that shows between 25% and 90% data path availability, and an unavailable load balancer is one with less than 25% data path availability, over a two minute period. These same steps can be taken to investigate the failure you see in any health probe status or data path availability alerts you have configured. We will explore the case where we've check our resource health and found our load balancer to be unavailable with a data path availability of 0% - our service is completely down.

The first step we should take is to note down the time that this occured and navigate to the detailed metrics view of our load balancer insights sub-blade either via our load balancer resource or the link in your resource health message.  Navigate to the Frontend and Backend availability tab and review a thirty minute window of this time period. If we see that our data path availability has been 0% we know that there is an issue preventing traffic for all of our load balancing and inbound NAT rules and can see how long this impact has lasted. 

The next place we need to look is our health probe status metric to determine whether this is because we have no healthy backend instances to serve traffic. If we check the health probe status and see that we do in fact have one health backend instance for all of our load balancing and inbound rules, we know that there is no issue with our configuration. This scenario indicates an Azure platform issue, while these are rare do not fret if you encounter them as an automated alert is sent to our team to resolve any and all platform issues as soon as possible.

### Diagnose health probe failures
Let's say we check our health probe status and find out that all instances are showing as unhealthy. This explains why our data path is unavailable as traffic has nowhere to go. In the vast majority of cases this is due to a configuration error. We should then go through the following checklist to rule out common configuration errors:
* Check the CPU utilization for your resources to check if your instances are in fact healthy
* If using an HTTP or HTTPS probe check if the application is healthy and responsive
* Review the Network Security Groups applied to our backend resources: ensure that there are rules which are of a higher priority than AllowAzureLoadBalancerInBound(which is automatically created) that will block the health probe
  * If you find this is the case, move the existing Allow rule or create a new high priority rule which will allow AzureLoadBalancer source traffic
* Check your OS: ensure you are listening on the probe port and review your OS firewall rules to ensure you are not blocking the probe traffic originating from IP address 168.63.129.16
* Do not place a firewall NVA VM in the backend pool of the load balancer, use [user-defined routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) to route traffic to backend instances through the firewall
* Ensure you are using the right protocol, if using HTTP to probe listening for a non-HTTP application this will fail

If you have gone through this checklist and are still finding health probe failures, there may be platform issues impacting the probe service for your instances. In this case Azure has your back and an automated alert is sent to our  team to resolve any and all platform issues as soon as possible.

## Next steps

* [Learn more about the Azure Load Balancer health probe](load-balancer-custom-probe-overview.md)
* [Learn more about Azure Load Balancer metrics](load-balancer-standard-diagnostics.md)


