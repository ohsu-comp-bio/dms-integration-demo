FROM phusion/passenger-full

# Install PHP5
RUN apt-get update
RUN apt-get install -y php5-fpm wget unzip php5-cli php5-pgsql

# Install nginx
RUN \
  add-apt-repository -y ppa:nginx/stable && \
  apt-get update && \
  apt-get install -y nginx && \
  rm -rf /var/lib/apt/lists/* && \
  echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
  chown -R www-data:www-data /var/lib/nginx

# Download mywebsql
RUN wget -q http://downloads.sourceforge.net/project/mywebsql/stable/mywebsql-3.6.zip?use_mirror=iweb -O /opt/mywebsql.zip
RUN cd /opt && unzip mywebsql.zip && rm mywebsql.zip

ADD . /

# Clean up configurations.
RUN ln -s /etc/nginx/sites-available/mywebsql.conf /etc/nginx/sites-enabled/mywebsql.conf
RUN rm /etc/nginx/sites-enabled/default /etc/php5/fpm/pool.d/www.conf

RUN mkdir /site

EXPOSE 80

CMD cp -r /site/. / && /sbin/my_init
