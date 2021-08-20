---
title: "covid19-datalake"
date: 2020-03-28
draft: true
tags: ["big-data", "distributed-systems"]
---
### Covid19 datalake
Due to the Covid19 emergency, a huge number of projects and interest on related data has grown up. I'm involved on building up a datalake of a wide range of data linked to the Covid19 pandemic. I started to collect both data on Italy and World cases spread from the official italian [Protezione Civile data repository](https://github.com/pcm-dpc/COVID-19) and the global Johns Hopkins repository(https://github.com/CSSEGISandData/COVID-19). Moreover i started a tweets collection on selected hashtags to build up a huge set of documents (tweets) to allow possile future sentiment analysis. Data are collected with a python script that listens each tweet with specified hashtag as soon as is published. All data are stored in the ENEA mongoDB cluster to allow fast and simple queries in a unique environment. Even if the data can be found online from other sources, in this way it is possible to have a single place to refer to, facilitating the work of researchers.
