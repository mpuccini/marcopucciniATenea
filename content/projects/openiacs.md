---
title: "openiacs"
date: 2020-12-01
draft: true
tags: ["semantic-web"]
---

### OpenIACS

#### LOD endpoint

OpenIACS is a project that aims to build up an Open LOD platform based on HPC capabilities for Integrated Administration of Common Agriculture Policy. Fice countries are in the project: Greece, Italy, Lithuania, Poland and Spain. ENEA is involved due for his *HPC* resources and ICT know-how. The first task was to provide to the project, an available LOD triplestore endpoint. I need to build up a Virtuoso instance on a dedicated VM on the VMWare ENEA cluster. To do this job i wrote a [fabric](https://www.fabfile.org/) script to have a reproducible result. This script is stored in dedicated [repository](https://gitlab.brindisi.enea.it/marco.puccini/installvirtuoso) on the ENEA gitlab instance.

#### Containers optimization

The complex architecture of the platform consists on different components. Among this are the ETL pipelines to convert shape files into LOD tiples and upload that on Virtuoso. Those ETL pipelines are been developed by the Polish partner of the project, the Data Analytics and Semantics Department in Poznan Supercomputing and Networking Center. My work was to optimize the containerization of such software, providing a lighter and faster image building file both for Docker and Singularity. The most useful optimization cames with using a *multi-stage building* approach. In the following the results, where the are highlighted the multi-stage building implementation.

##### Dockerfile

```docker {linenos=inline,hl_lines=[1, "24-26"],linenostart=1}
FROM maven:3.6.3-jdk-8-slim AS maven

WORKDIR /src

COPY /src /src
RUN apt-get update \
    && apt install -y git

# cloning rmlmapper tool repository
RUN git clone --branch v4.9.0 https://github.com/RMLio/rmlmapper-java.git utils/rmlmapper

# building rmlmapper jar using maven.
RUN cd utils/rmlmapper/ \
    && mvn install -DskipTests

# cloning geotriples tool repository
RUN git clone https://github.com/rapw3k/GeoTriples.git utils/GeoTriples

# building geotriples jar using maven.
RUN cd utils/GeoTriples \
    && mvn package


FROM python:3.7-buster

COPY --from=maven /usr/local/openjdk-8 /usr/local/openjdk-8
ENV JAVA_HOME /usr/local/openjdk-8
RUN update-alternatives --install /usr/bin/java java /usr/local/openjdk-8/bin/java 1
COPY --from=maven /src /src
COPY requirements.txt /
COPY .env /src/.
RUN mkdir -p /root/.ssh && touch /root/.ssh/known_hosts

RUN python3 -m pip install --upgrade pip
RUN pip3 install -r /requirements.txt
```
In `line 1` the image that contains maven environment is downloaded first to build the container with the proper environment to build the java libraries needed for the ETL. In lines `24-26` the python image is downloaded to build a container with python environment and the builded java libraries are copied from the previous container.

##### Singularity.def

The same stuff are done in the highlighted lines below.

```yaml {linenos=inline,hl_lines=["1-3", "26-34"],linenostart=1}
Bootstrap: docker
From: maven:3.6.3-jdk-8-slim
Stage: maven

%files
  src /src

%post
  apt-get update
  apt install -y git

  # cloning rmlmapper tool repository
  git clone --branch v4.9.0 https://github.com/RMLio/rmlmapper-java.git /src/utils/rmlmapper

  # building rmlmapper jar using maven.
  cd /src/utils/rmlmapper/ && mvn install -DskipTests

  # cloning geotriples tool repository
  git clone https://github.com/rapw3k/GeoTriples.git /src/utils/GeoTriples

  # building geotriples jar using maven.
  cd /src/utils/GeoTriples && mvn package



Bootstrap: docker
From: python:3.7-buster
Stage: final



%files from maven
  /usr/local/openjdk-8 /usr/local/openjdk-8
  /src /src

%files
  requirements.txt
  .env /src/.


%environment
  JAVA_HOME=/usr/local/openjdk-8

%post
  update-alternatives --install /usr/bin/java java /usr/local/openjdk-8/bin/java 1
  mkdir -p /root/.ssh && touch /root/.ssh/known_hosts
  python3 -m pip install --upgrade pip
  pip3 install -r requirements.txt

%runscript
  exec "$@"

```

##### Security and scalability improvmentes

The great advantage of the container approach is to be easily transportable and scalable. That mainly due to its *statelesses*, or to be independent from configurations and immutable. Thanks to this, you can run the same container images multiple times, with different configurations, without the needs to *step inside* it and change nothing. So, to allow this, all the mutable stuff needs to be *outside* the container image. One another important work carried out is to re-factor the code organization to fill the principle descibed above. Following that logic, all the pipeline source code are being organized inside a `src` folder like for the configurations, inside `cfg` folder. Other general configurations values are passed inside the container as environmental variable, collected in a `.env` file. Moreover, to manage properly how to run containers, both for Docker and Singularity, with stuff like mounting external volumes, passing variables, a shell script is being developed.

```bash
#! /bin/bash
# author: Marco Puccini (marco.puccini@enea.it)

echo "Check for logs and temp folders"
for dir in logs temp
do
	if [ ! -d ${dir} ]
	then
		mkdir ${dir}
		echo "Create ${dir} folder"
	else
		echo "Folder ${dir} already exists"
	fi
done
echo "############################################################"

if [ "$2" == "--docker" ] || [ "$2" == "-d" ]
then
	echo "Run ${1} in docker container"
	echo "############################################################"
	sudo docker run -ti \
		-v /home/virtuoso/pipelines/DATA:/DATA \
		-v $PWD/cfg:/src/cfg \
		-v $PWD/logs:/src/logs \
		-v $PWD/temp:/src/temp \
		pipeline-etl \
		"${1}"
elif [ "$2" == "--singularity" ] || [ "$2" == "-s" ]
then
	echo "Run ${1} in singularity container"
	echo "############################################################"
	singularity run \
		-B /home/virtuoso/pipelines/DATA/:/DATA \
		-B cfg/:/src/cfg \
		-B logs:/src/logs \
		-B temp/:/src/temp \
		pipeline-etl.sif \
		"${1}"
elif [ -z "$2" ]
then
	echo "Missing parameter"
	echo "Usage: runContainer.sh <pipeline> [--docker|--singularity]"
	echo "############################################################"
else
	echo "Unknown option: ${2}"
	echo "Usage: runContainer.sh <pipeline> [--docker|--singularity]"
	echo "############################################################"
fi
```

This is particularly useful because this pipelines needs to be runned in a **HPC environment**, like IBM LSF in ENEA or SLURM for other project partners, mainly designed to work with batch processing. With the script above you just need to gain execution rights to the script

```bash
chmod +x runContainer.sh
```
and run it, passing as arguments the proper pipeline and a flag to choose Docker or Singularity/Apptainer.

```bash
chmod +x runContainer.sh
./runContainer.sh <pipeline> [--docker|--singularity]
```
or
```bash
./runContainer.sh <pipeline> [-d|-s]
```
The `pipeline` is defined in a file according to the documentation provided from Poznan developers. You can define different pipelines with different files. Some examples are collected in the `pipelines` folder.

The complete source code is accessible at the [repository](https://gitlab.brindisi.enea.it/marco.puccini/openiacs-pipelines).
