---
title: "enea-mongodb"
date: 2019
draft: true
tags: ["big-data", "distributed-systems"]
---

### ENEA mongoDB
Among the big field of noSQL databases, [mongoDB](https://www.mongodb.com/) is the most widely used open source technology of his type, especially for Big Data storage and mnagment. ENEA start a collaboration with me to evaluate such technology and to build up a cluster installation of the *Community Edition*. Due to the common [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem) tradeoffs, mongoDB has a complex architecture to guarantee Consistency and (of course) Partition failures, with an high ratio of Accessibility too. This architecture consists of 3 logical entities:
											
* Shard
* Config Server
* Router
														
A **Shard** is where data are stored and its name came from the *sharding* function that it perform. The sharding is the operation to divide data into smallest parts that being distributed to many Shards. Every part of fragmented data has an identification key, so it is possibile to rebuild the entire data once is querried. This allow the system to scale *horizzontally* that is the ability to raise performances adding more hosts (physical or virtual) instead of upgrading each single host hardware.  
The **Config Server** is the one who take all the cluster infos, such as the shards keys, the operations of data and more.  
The **Router** is the one who *routes* the traffic within the cluster, so if you need to query the DB, you **must** interact with him.  
To allow data redundancy, the shards and the config server must run on ($2n+1$) hosts each, so all the information that they have, can be replicated in a consistent way, due to the rules of *Consensus* in case of partition failures. For this reason, the minimum number of hosts that a shard and/or a Config server need to installed on is 3. This set of hosts is named *Replica Set*. So you need to build both the Shards and the Config Servers on Replica Sets. In each Replica Set there are roles:
														
* Primary: the first to receive i/o operations
* Secondary: where data replications are copied asynchronously
* Arbiter: partecipate on electing new Primary in case of failures, but does not hold data.
																	
The ENEA cluster is builded on 4 physical machine with this logical sheme:  
![](/puccini/imgs/sharded-cluster-arch.png?raw=true)

where there are 3 hosts that serves 1 Shard and 1 Config Server, and 1 hosts for the Router according to this schema for hostname roles (showed in a *yaml* way):
```yaml
cfgRS:
	hostnames:
		- cresco-mgdb01
		- cresco-mgdb02
		- cresco-mgdb03
	port: 27019

routers:
	hostnames:
		- cresco-mgdb04
	port: 27017

shards:
	hostnames:
		sh0:
			- cresco-mgdb03
			- cresco-mgdb02
			- cresco-mgdb01																 
	port: 27018
```
This schema shows also the ports where instances talks with. To access the mongoDB you need the proper authorizations.  

>As you can see from the architecture schema, you might think it's useless to have only one shard. Indeed this is a Development configuration according to mongoDB manual too. This is due to the extraordinary occurrence of Covid-19 pandemic explosion concurrent to the cluster setup. So other hosts they were expected to serve the mongoDB cluster are not installed in time. **Despite this, the architecture is ready at anytime to be expanded with new shards as soon is it will be possible again**.
