# Data backlog and it's impact on error budget

We often get asked how Nobl9 handles data backlog in various cases; hence let’s spend some time to explore different scenarios and how to address them. 


## 1. How does backlogging work with Nobl9 agents? (for example network issues)

  This is slightly different between the data sources; However, in a more general sense, if an agent can’t reach the N9 data intake, it caches using FIFO method and retries indefinitely. If the lack of connection takes too long, it may exhaust the cache. It is also worth noting that the cache is user configurable by how much memory is allocated to the container. 
This lack of data could impact the Error Budget but our product team is working to address this impact and shine more lights on it in our future releases.


## 2. What if there is a data outage on the data source?

  In short, if the data source is out then Nobl9 won’t get data and the error budget will be impacted. 
If the agent keeps running, it will try to catch up after reconnecting (This does vary by data source). If the agent restarts (says it's relocated) then it's possible it would stop retrying. Our integration mechanism (querying APIs or metrics systems) is naturally somewhat susceptible to outages either between the agent and the metrics system (including outage of the metrics system) or between the agent and Nobl9. 

**In summary to quote Alex Hidalgo,**
>“SLOs are essentially always approximations. As long as there isn’t too much missing data you’ll still get a clear picture over long time windows. Hiccups are bound to happen. Which is why we have SLOs in the first place”
