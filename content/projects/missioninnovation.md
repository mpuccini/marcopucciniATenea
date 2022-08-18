---
title: "mission-innovation"
date: 2021-05-13
draft: true
tags: ["material-sciences", "restful-api", "deep-learning"]
---

### Mission Innovation

As part of the [Mission Innovation](http://mission-innovation.net/) project, a global initiative to research and develop clean energy, there is the design and development of IEMAP portal to accelerate research of innovative material for hydrigen batteries. Such portal aims to collect data from partners both from experiments and computational simulations, helping researcher to find new materials providing analisys tools enhanced with machine learning algorithms.
The ENEA ICT division in involved in this WP. A first data modelization has carried out, trying to design a proper data and metadata model to allow interoperability and implementation of FAIR principles. In this To make them accessible a RESTful API service is under development, reachable at this link: [IEMAP APIs](https://ai4mat.enea.it/docs). The source code is under open source license and accessible at a dedicated [Github Repository](https://github.com/ai4mat/iemap-api), while the official [IEMAP APIs documentation](https://iemap-api.readthedocs.io/en/latest/index.html) is provided too.
The API works over the **IEMAP DataLake** that collects metadanta into the ENEA mongoDB cluster serialized as JSON documents, while the raw data are stored on the ENEA Ceph cluster, exposed as a CephFS file system. The data acquisition to storage pipeline overview is in the following:
![](/puccini/imgs/IEMAP-DataLake.png?raw=true)
