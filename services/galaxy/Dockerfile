FROM phusion/passenger-full

ENV GALAXY_EGGS_PATH /data/galaxy/eggs
ENV GALAXY_ROOT /opt/galaxy

RUN mkdir $GALAXY_ROOT

WORKDIR $GALAXY_ROOT

RUN apt-get update
RUN apt-get install -y \
  build-essential \
  git \
  libbz2-dev \
  libreadline-dev \
  libsqlite3-dev \
  libssl-dev \
  make \
  zlib1g-dev

# Install pip
RUN curl https://bootstrap.pypa.io/get-pip.py > /opt/get-pip.py
RUN python /opt/get-pip.py && rm /opt/get-pip.py

# Create a virtual environment
RUN pip install virtualenv && virtualenv .venv

ADD . /

# Copy our config over
RUN cp /etc/galaxy/galaxy.ini $GALAXY_ROOT/config/galaxy.ini
RUN cp /usr/local/bin/galaxy_environment.sh $GALAXY_ROOT/contrib/intel_env.sh

# Use Bash as our shell.
RUN update-alternatives --install /bin/sh bash /bin/bash 1

EXPOSE 8080
