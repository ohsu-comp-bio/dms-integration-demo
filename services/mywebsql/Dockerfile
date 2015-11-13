FROM phusion/passenger-full

# Install PHP5
RUN apt-get update
RUN apt-get install -y php5-fpm wget unzip php5-cli php5-pgsql

# Download mywebsql
RUN wget -q http://downloads.sourceforge.net/project/mywebsql/stable/mywebsql-3.6.zip?use_mirror=iweb -O /opt/mywebsql.zip
RUN cd /opt && unzip mywebsql.zip && rm mywebsql.zip

ADD . /

# Enable nginx
RUN rm -f /etc/service/nginx/down
RUN rm /etc/nginx/sites-enabled/default

RUN rm /etc/php5/fpm/pool.d/www.conf

RUN mkdir /site

EXPOSE 80

CMD cp -r /site/. / && /sbin/my_init
