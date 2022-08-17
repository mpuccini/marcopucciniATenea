---
title: "DTECH"
date: 2022-01-01
draft: true
tags: ["cultural-heritage"]
---

On January 2022 DTECH project started. DTECH (Digital Twin Environment for Cultural Heritage) is a winning project in the Tecnological District for Cultural Heritage of the italian Lazio Region [DTC](https://dtclazio.it/) (Distretto Tecnologico Beni e Attività Culturali), with a parnership of ENEA, University Roma 3 Department of Architecture and Department of Geography and the IT private company [Overit](https://www.overit.it/).

The main goal of the project is to develop an integrated system to manage Digital Twins object such as point clouds, 3D reconstructions, HBIM, AR and VR models and GIS data in a interoperable web based platform. Such platform wants to be an innovative tool addressed to cultural assets manager (museums, institutions, ...) that integrates a data management environment with a set of advanced viewer to store, organize, enrich, and (eventually) share digital replicas of physical cultural assets.

The most innovative solution reside in the software background architecture that is a microservices, cloud native and open source design. That particular architecture allow to give to assets manager to work with his digital objects maintaining ownership due to the fact that each user (assets manager) has his own installation with a dedicate archive both for metadata (database) and object (storage). The assets owner may choose to install such tool on cloud or on bare metal hardware of his own.

A central database service, provided by ENEA, will allow to choose to share a part or the whole set of data with other, in a sort of distributed database, collecting only the choosen object metadata, building a *shared archive* of these.

ENEA is involved in the main part of the project, in particular in the WP3 activities which concern the design and development of the most important software components of the product that is named *Kore*. This components includes  in the backend the archiving and storage systems, the authentication and users permissions managment and the frontend user interface with some basic viewer. The Kore will be released with an Open Source licence. The WP3 is in partnership with [Tecnostudi Ambiente](https://www.tecnostudiambiente.it/) company.

On this open source core, Overit will hook and integrate advanced proprietary viewers services for sale.

DTECH project is the natural evolution of the previous [Open DigitalTwins PoC](https://www.afs.enea.it/puccini/projects/opendigitaltwins/).
