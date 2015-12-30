FROM phusion/passenger-full

RUN apt-get update

RUN apt-get install -y openjdk-8-jdk maven wget

# Use Java 1.8
RUN update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-8-openjdk-amd64/bin/java 1
RUN update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/bin/java

ENV CB_VERSION=4.0.0 \
    CB_RELEASE_URL=http://packages.couchbase.com/releases \
    CB_PACKAGE=couchbase-server-community_4.0.0-ubuntu12.04_amd64.deb \
    CB_SHA256=404007eaedc3d01997eea800fcce0d0a0339bc3ab79c1c48741210f435c719f0 \
    PATH=$PATH:/opt/couchbase/bin:/opt/couchbase/bin/tools:/opt/couchbase/bin/install \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/couchbase/lib

# Install couchbase
RUN wget -N $CB_RELEASE_URL/$CB_VERSION/$CB_PACKAGE && \
    echo "$CB_SHA256  $CB_PACKAGE" | sha256sum -c - && \
    dpkg -i ./$CB_PACKAGE && rm -f ./$CB_PACKAGE

RUN ln -s /data/couchbase/var /opt/couchbase/var

ADD . /

RUN cd /opt/app_repository/appmanager && mvn package -Dmaven.test.skip=true
RUN cd /opt/app_repository/containermanager && mvn package -Dmaven.test.skip=true

CMD cp -r /site/. / && /sbin/my_init

EXPOSE 8080 8081
