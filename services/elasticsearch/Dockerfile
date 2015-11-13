FROM phusion/passenger-full

RUN apt-get update
RUN apt-get install -y openjdk-8-jre wget

ENV ES_PKG_NAME elasticsearch-1.5.0

# Install Elasticsearch.
RUN \
  cd / && \
  wget https://download.elasticsearch.org/elasticsearch/elasticsearch/$ES_PKG_NAME.tar.gz && \
  tar xvzf $ES_PKG_NAME.tar.gz && \
  rm -f $ES_PKG_NAME.tar.gz && \
  mv /$ES_PKG_NAME /opt/elasticsearch

# Define working directory.
WORKDIR /opt/elasticsearch

# Expose ports.
#   - 9200: HTTP
#   - 9300: transport
EXPOSE 9200
EXPOSE 9300

ADD . / 

CMD cp -r /site/. / && /sbin/my_init
