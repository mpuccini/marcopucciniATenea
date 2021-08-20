---
title: "ecodigit"
date: 2020-03-28
draft: true
tags: ["cultural-heritage", "ict", "semantic-web"]
---

### Ecodigit
[Ecodigit](http://ecodigit.dtclazio.it/) was a project funded by the Regione Lazio for the [DTC](https://dtclazio.it/) (District of Technologies and Culture) Centre of Excellence. It aims to project and develop the middleware component of the DTC digital ecosystem for cultural heritage. In particular ENEA was invloved to build up the *Proof of Concept*. It started in the **November of 2018** and ended in the **January of 2020**. The main task was to build up a software component to make interoperable and dynamic the DTC graph of knowledge with advanced visualizations services. For this purpose an extension of the [ISTI-CNR](https://www.isti.cnr.it/) [3DHOP](http://3dhop.net/) was carried out. 3DHOP is an open source 3D viewer that works in the browser client with a set of javascript libraries that builds up the visualization web page for a 3D model (.ply, .obj or compressed .nxz). It's a great tool, used by many progects and institutions. The main limit is that it needs to be builded up for **each** model. so if you have a database, you have to design, build and store a page for each model with the model itself. ENEA design an extension of the [Arco](http://wit.istc.cnr.it/arco) ontologies to make possible the 3D model desctiption with just few characteristics. Due to this it was possible to design and develop the 3DHOP extension that allow to build up dinamically the viewer page *around* a 3D model queryed on the database. This extension ([3DHOP extension repository](https://github.com/ecodigit/3dhop-react)) consist of two PHP components:
							
* *Gatherer*: makes queries on the database after a 3D object has requested. This query gives the 3D objectURI and his desctipting values, passing them to the next component.
* *Assembler*: takes all the 3D object info from the previous component and build up the viewer page.
									
In the Figure below is showed the software architecture of how those components works
![](/puccini/imgs/3dhop_ecodigit.png?raw=true)
Figure 1 - 3DHOP Ecodigit extension architecture.  

While the javascript librearies works client-side to renderize and visualize the model, all this stuff works server-side. Due to the two separated component the gathere could be rewritten to interoperate with other kind of database or, better, extend the software with different gatherer. One for each kind database. Whereas the assembler component is able to build the page with the proper activated functions related to the model characteristics. The prototype is reacheable (until 2025) at this [link](http://150.146.207.67/prototipo/).
> **Important note**: in this prototype only two objects are queryable:				 
> * Trono Corsini: a single object scaled in *cm*
> * Porta latina: a multiple oblect with one linked hotspot scaled in *m*
>											
> So, you can test the PoC by typing **ONLY** Trono Corsini or Porta latina. Otherwise you'll don't get any results.
