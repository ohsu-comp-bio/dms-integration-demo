FROM phusion/passenger-full

RUN apt-get update
RUN apt-get install -y openjdk-8-jre maven

RUN useradd condor --shell /bin/bash --create-home && \
  usermod -a -G sudo condor && \
  echo 'ALL ALL = (ALL) NOPASSWD: ALL' >> /etc/sudoers && \
  echo 'condor:secret' | chpasswd

RUN curl http://parrot.cs.wisc.edu//symlink/20151119031502/8/8.4/8.4.2/296564b11ad1ab25b796ea1616ae397a/condor-8.4.2-x86_64_Ubuntu14-stripped.tar.gz > /opt/condor.tar.gz && \
  cd /opt/ && \
  tar -xf condor.tar.gz && \
  rm condor.tar.gz && \
  mv condor-8.4.2-x86_64_Ubuntu14-stripped condor

RUN cd /opt/condor && ./condor_install --prefix=/usr/local --owner condor

ADD . /
