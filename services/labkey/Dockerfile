FROM phusion/passenger-full

EXPOSE 8080

# Install Java + Tomcat
RUN apt-get update
RUN apt-get install -y openjdk-8-jre

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

# see https://www.apache.org/dist/tomcat/tomcat-8/KEYS
RUN gpg --keyserver pool.sks-keyservers.net --recv-keys \
  05AB33110949707C93A279E3D3EFE6B686867BA6 \
  07E48665A34DCAFAE522E5E6266191C37C037D42 \
  47309207D818FFD8DCD3F83F1931D684307A10A5 \
  541FBE7D8F78B25E055DDEE13C370389288584E7 \
  61B832AC2F1C5A90F0F9B00A1C506407564C17A3 \
  713DA88BE50911535FE716F5208B0AB1D63011C7 \
  79F7026C690BAA50B92CD8B66A3AD3F4F22C4FED \
  9BA44C2621385CB966EBA586F72C284D731FABEE \
  A27677289986DB50844682F8ACB77FC2E86E29AC \
  A9C5DF4D22E99998D9875A5110C01C5A2F6059E7 \
  DCFD35E0BF8CA7344752DE8B6FB21E8933C60243 \
  F3A04C595DB5B6A5F1ECA43E3B7BBB100D811BBE \
  F7DA48BB64BCB84ECBA7EE6935CD23C10D498E23

ENV TOMCAT_MAJOR 7
ENV TOMCAT_VERSION 7.0.67
ENV TOMCAT_TGZ_URL https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz

RUN set -x \
  && curl -fSL "$TOMCAT_TGZ_URL" -o tomcat.tar.gz \
  && curl -fSL "$TOMCAT_TGZ_URL.asc" -o tomcat.tar.gz.asc \
  && gpg --verify tomcat.tar.gz.asc \
  && tar -xvf tomcat.tar.gz --strip-components=1 \
  && rm bin/*.bat \
  && rm tomcat.tar.gz*

# Install pip + envtpl for rendering Jinja2 templates from environment variables
RUN curl https://bootstrap.pypa.io/get-pip.py > /opt/get-pip.py
RUN python3 /opt/get-pip.py && rm /opt/get-pip.py
RUN pip install envtpl

# Download Labkey
RUN curl http://labkey.s3.amazonaws.com/downloads/general/r/15.3/LabKey15.3-41116.23-community-bin.tar.gz > /opt/labkey.tar.gz

# Extract Labkey
RUN cd /opt && \
  tar -xf labkey.tar.gz && \
  mv LabKey15.3-41116.23-community-bin labkey && \
  rm labkey.tar.gz

RUN cp /opt/labkey/tomcat-lib/* /usr/local/tomcat/lib

# Install a newer version of Apache Commons Validator to support more TLDs. Note
# we have to install it to 1.4.0.jar since Labkey will load this jar file by name.
RUN curl http://central.maven.org/maven2/commons-validator/commons-validator/1.4.1/commons-validator-1.4.1.jar > /opt/labkey/labkeywebapp/WEB-INF/lib/commons-validator-1.4.0.jar

ENV APPDOCBASE /opt/labkey/labkeywebapp

RUN rm -Rf /usr/local/tomcat/webapps/ROOT

WORKDIR /opt/labkey

RUN mkdir /site

ADD . / 

# Install Ruby dependencies
RUN gem install bundler
RUN cd /etc/labkey && bundle install

# Download and extract extra modules.
RUN mkdir /opt/labkey/externalModules && \
  cd /opt && \
  curl http://labkey.s3.amazonaws.com/downloads/general/r/15.3/LabKey15.3-41116.23-ExtraModules.tar.gz > labkey-extramodules.tar.gz && \
  tar -xvvf labkey-extramodules.tar.gz && \
  cp LabKey15.3-41116.23-bin/modules/* /opt/labkey/externalModules && \
  rm -Rf labkey-extramodules.tar.gz LabKey15.3-41116.23-bin

RUN cp /opt/ccc_cf_client/build/ccc_client.module /opt/labkey/externalModules

CMD cp -rf /site/. / && /sbin/my_init
