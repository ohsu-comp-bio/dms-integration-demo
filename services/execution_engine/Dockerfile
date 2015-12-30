FROM phusion/passenger-full

# Use Java 1.8
RUN update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-8-openjdk-amd64/bin/java 1
RUN update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/bin/java

# Install Scala
RUN curl http://www.scala-lang.org/files/archive/scala-2.11.7.deb > scala-2.11.7.deb
RUN dpkg -i scala-2.11.7.deb
RUN rm -f scala-2.11.7.deb

# Install SBT
RUN echo "deb https://dl.bintray.com/sbt/debian /" >> /etc/apt/sources.list.d/sbt.list
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823
RUN apt-get update
RUN apt-get install -y sbt maven libboost-dev libboost-test-dev \
  libboost-program-options-dev libevent-dev automake libtool flex bison \
  pkg-config g++ libssl-dev openjdk-8-jdk htcondor-dev

# Install Thrift
RUN curl http://apache.cs.utah.edu/thrift/0.9.3/thrift-0.9.3.tar.gz > /opt/thrift-0.9.3.tar.gz
RUN cd /opt && tar -xf thrift-0.9.3.tar.gz
RUN cd /opt/thrift-0.9.3 && ./configure --without-ruby && make && make install

# Install MongoDB

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r mongodb && useradd -r -g mongodb mongodb

RUN apt-get update && \
  apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    numactl \
  && rm -rf /var/lib/apt/lists/*

# grab gosu for easy step-down from root
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
RUN curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.6/gosu-$(dpkg --print-architecture)" \
  && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.6/gosu-$(dpkg --print-architecture).asc" \
  && gpg --verify /usr/local/bin/gosu.asc \
  && rm /usr/local/bin/gosu.asc \
  && chmod +x /usr/local/bin/gosu

# gpg: key 7F0CEB10: public key "Richard Kreuter <richard@10gen.com>" imported
RUN apt-key adv --keyserver ha.pool.sks-keyservers.net --recv-keys 492EAFE8CD016A07919F1D2B9ECBEC467F0CEB10

ENV MONGO_MAJOR 3.0
ENV MONGO_VERSION 3.0.8

RUN echo "deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/$MONGO_MAJOR main" > /etc/apt/sources.list.d/mongodb-org.list

RUN set -x \
  && apt-get update \
  && apt-get install -y \
    mongodb-org=$MONGO_VERSION \
    mongodb-org-server=$MONGO_VERSION \
    mongodb-org-shell=$MONGO_VERSION \
    mongodb-org-mongos=$MONGO_VERSION \
    mongodb-org-tools=$MONGO_VERSION \
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf /var/lib/mongodb \
  && mv /etc/mongod.conf /etc/mongod.conf.orig

RUN mkdir -p /data/mongodb && chown -R mongodb:mongodb /data/mongodb

EXPOSE 27017

# Add project sources
ADD . /

# Needed to pulling SBT dependencies.
RUN update-ca-certificates -f 

# Build components
RUN cd /opt/execution_engine/agent && sbt assembly
RUN cd /opt/execution_engine/dispatcher && sbt assembly
RUN cd /opt/execution_engine/dts && sbt assembly

# Build resource manager
RUN cd /opt/execution_engine/resource_manager && mvn install:install-file -Dfile=./libs/htcondor-1.0.jar -DgroupId=condor -DartifactId=htcondor -Dversion=1.0 -Dpackaging=jar
RUN cd /opt/execution_engine/resource_manager && mvn clean && mvn package -Dmaven.test.skip=true

# Copy over assets to Cromwell project
RUN cp -r /opt/execution_engine/cromwell/src /opt/cromwell/
RUN cp /opt/execution_engine/cromwell/build.sbt /opt/cromwell/
RUN cp /opt/execution_engine/cromwell/scalastyle-config.xml /opt/cromwell/
RUN cp /opt/execution_engine/cromwell/project/plugins.sbt /opt/cromwell/project/
RUN cp /opt/execution_engine/dispatcher/target/scala-2.11/ccc_dispatcher.jar /opt/cromwell/lib/

# Build Cromwell
RUN cd /opt/cromwell && rm assembly.sbt && sbt assembly

# Install pip + envtpl for rendering Jinja2 templates from environment variables
RUN curl https://bootstrap.pypa.io/get-pip.py > /opt/get-pip.py
RUN python3 /opt/get-pip.py && rm /opt/get-pip.py
RUN pip install envtpl

CMD cp -r /site/. / && /sbin/my_init
