---
title: "openiacs"
date: 2020-12-01
draft: true
tags: ["semantic-web"]
---

### OpenIACS

OpenIACS is a project that aims to build up an Open LOD platform based on HPC capabilities for Integrated Administration of Common Agriculture Policy. Fice countries are in the project: Greece, Italy, Lithuania, Poland and Spain. ENEA is involved due for his *HPC* resources and ICT know-how. The first task was to provide to the project, an available LOD triplestore endpoint. I need to build up a Virtuoso instance on a dedicated VM on the VMWare ENEA cluster. To do this job i wrote a [fabric](https://www.fabfile.org/) script to have a reproducible result. This script is stored in dedicated [repository](https://gitlab.brindisi.enea.it/marco.puccini/installvirtuoso) on the ENEA gitlab instance.

