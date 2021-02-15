---
title: "projects"
date: 2021-02-14T10:05:44+01:00
draft: true
---

## 2020
### M.I.A. Fashion (currently involved)
M.I.A. Fashion (link in italian), Models and Solutions for Artificial Intelligence in the Luxury Fashion sector, is a project that aims to design and develop an ICT platform to support business of an importat luxury fashion company. The final objective of the project is to create a hybrid digital ecosystem, identifying the right technological trade-off that allows to take full advantage of the opportunities presented by the technical-scientific innovation related to artificial intelligence systems aiming to:

* a better satisfaction of consumer's expectations
* an improvement of sales channels
* a growth of touch points with customers
* developing predictive analysis on consumer behavior during the purchase phase
* developing a guiding function in the strategies for the supply of products and services
* develop analyses aimed at supporting the creative and productive area of the Company
* develop security systems that guarantee the correct use of information data, avoiding external threats
							
In particular i'm working on design and build up the backend for collecting, storing and makes available data from social networks to Deep Learning models. Technologies adopted are, in the first instance, mongoDB for metadata storing and GPFS (IBM Spectrum) as distribuited file system, while Ceph is in testing.

							
### Covid19 datalake
Due to the Covid19 emergency, a huge number of projects and interest on related data has grown up. I'm involved on building up a datalake of a wide range of data linked to the Covid19 pandemic. I started to collect both data on Italy and World cases spread from the official italian Protezione Civile data repository and the global Johns Hopkins repository. Moreover i started a tweets collection on selected hashtags to build up a huge set of documents (tweets) to allow possile future sentiment analysis. Data are collected with a python script that listens each tweet with specified hashtag as soon as is published. All data are stored in the ENEA mongoDB cluster to allow fast and simple queries in a unique environment. Even if the data can be found online from other sources, in this way it is possible to have a single place to refer to, facilitating the work of researchers.

							
## 2019
### Ecodigit
Ecodigit was a project funded by the Regione Lazio for the DTC (District of Technologies and Culture) Centre of Excellence. It aims to project and develop the middleware component of the DTC digital ecosystem for cultural heritage. In particular ENEA was invloved to build up the Proof of Concept. It started in the November of 2018 and ended in the January of 2020. The main task was to build up a software component to make interoperable and dynamic the DTC graph of knowledge with advanced visualizations services. For this purpose an extension of the ISTI-CNR 3DHOP was carried out. 3DHOP is an open source 3D viewer that works in the browser client with a set of javascript libraries that builds up the visualization web page for a 3D model (.ply, .obj or compressed .nxz). It's a great tool, used by many progects and institutions. The main limit is that it needs to be builded up for each model. so if you have a database, you have to design, build and store a page for each model with the model itself. ENEA design an extension of the Arco ontologies to make possible the 3D model desctiption with just few characteristics. Due to this it was possible to design and develop the 3DHOP extension that allow to build up dinamically the viewer page around a 3D model queryed on the database. This extension (3DHOP extension repository) consist of two PHP components:
							
* *Gatherer*: makes queries on the database after a 3D object has requested. This query gives the 3D objectURI and his desctipting values, passing them to the next component.
* *Assembler*: takes all the 3D object info from the previous component and build up the viewer page.
									
In the Figure below is showed the software architecture of how those components works

Figure 1 - 3DHOP Ecodigit extension architecture.
While the javascript librearies works client-side to renderize and visualize the model, all this stuff works server-side. Due to the two separated component the gathere could be rewritten to interoperate with other kind of database or, better, extend the software with different gatherer. One for each kind database. Whereas the assembler component is able to build the page with the proper activated functions related to the model characteristics. The prototype is reacheable (until 2025) at this link.
> **Important note**: in this prototype only two objects are queryable:				 
> * Trono Corsini: a single object scaled in *cm*
> * Porta latina: a multiple oblect with one linked hotspot scaled in *m*
>											
> So, you can test the PoC by typing ONLY Trono Corsini or Porta latina. Otherwise you'll don't get any results.

											
### ENEA mongoDB
Among the big field of noSQL databases, mongoDB is the most widely used open source technology of his type, especially for Big Data storage and mnagment. ENEA start a collaboration with me to evaluate such technology and to build up a cluster installation of the Community Edition. Due to the common CAP theorem tradeoffs, mongoDB has a complex architecture to guarantee Consistency and (of course) Partition failures, with an high ratio of Accessibility too. This architecture consists of 3 logical entities:
											
* Shard
* Config Server
* Router
														
A **Shard** is where data are stored and its name came from the sharding function that it perform. The sharding is the operation to divide data into smallest parts that being distributed to many Shards. Every part of fragmented data has an identification key, so it is possibile to rebuild the entire data once is querried. This allow the system to scale horizzontally that is the ability to raise performances adding more hosts (physical or virtual) instead of upgrading each single host hardware.  
The **Config Server** is the one who take all the cluster infos, such as the shards keys, the operations of data and more.  
The **Router** is the one who routes the traffic within the cluster, so if you need to query the DB, you must interact with him.  
To allow data redundancy, the shards and the config server must run on (2n+1) hosts each, so all the information that they have, can be replicated in a consistent way, due to the rules of Consensus in case of partition failures. For this reason, the minimum number of hosts that a shard and/or a Config server need to installed on is 3. This set of hosts is named Replica Set. So you need to build both the Shards and the Config Servers on Replica Sets. In each Replica Set there are roles:
														
* Primary: the first to receive i/o operations
* Secondary: where data replications are copied asynchronously
* Arbiter: partecipate on electing new Primary in case of failures, but does not hold data.
																	
The ENEA cluster is builded on 4 physical machine with this locagical sheme:  
FIGURE  
where there are 3 hosts that serves 1 Shard and 1 Config Server, and 1 hosts for the Router according to this schema for hostname roles (showed in a yaml way):
```yaml																				 cfgRS:
	- cresco-mgdb01
	- cresco-mgdb02
	- cresco-mgdb03
routers:
	- cresco-mgdb04														
shards:
	sh0:
		- cresco-mgdb03
		- cresco-mgdb02
		- cresco-mgdb01																 
ports:																				     routers: 27017																		  shards: 27018																		   cfgRS: 27019
```
This schema shows also the ports where instances talks with. To access the mongoDB you need the proper authorizations.  

>As you can see from the architecture schema, you might think it's useless to have only one shard. Indeed this is a Development configuration according to mongoDB manual too. This is due to the extraordinary occurrence of Covid-19 pandemic explosion concurrent to the cluster setup. So other hosts they were expected to serve the mongoDB cluster are not installed in time. Despite this, the architecture is ready at anytime to be expanded with new shards as soon is it will be possible again.
