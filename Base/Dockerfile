FROM arm32v7/ubuntu:18.04
#================================================
# Customize sources for apt-get
#================================================
#RUN  echo "deb http://ports.ubuntu.com/ubuntu-ports xenial main universe\n" > /etc/apt/sources.list \
#  && echo "deb http://ports.ubuntu.com/ubuntu-ports xenial-updates main universe\n" >> /etc/apt/sources.list \
#  && echo "deb http://ports.ubuntu.com/ubuntu-ports xenial-security main universe\n" >> /etc/apt/sources.list

# No interactive frontend during docker build
ENV DEBIAN_FRONTEND=noninteractive \
    DEBCONF_NONINTERACTIVE_SEEN=true


#========================
# Miscellaneous packages
# Includes minimal runtime used for executing non GUI Java programs
#========================

RUN apt-get -qqy update \
 && apt-get -qqy --no-install-recommends install \
    ca-certificates \
 && apt-get -qqy --no-install-recommends install \
    bzip2 \
    tzdata \
    sudo \
    unzip \
    wget \
    jq \
    curl \
    supervisor \
    ca-certificates-java \
  && sh -c "/var/lib/dpkg/info/ca-certificates-java.postinst configure" \
  && apt-get -qqy --no-install-recommends install \
     openjdk-8-jre-headless \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/* \
  && sed -i 's/securerandom\.source=file:\/dev\/random/securerandom\.source=file:\/dev\/urandom/' ./usr/lib/jvm/java-8-openjdk-armhf/jre/lib/security/java.security

#===================
# Timezone settings
# Possible alternative: https://github.com/docker/docker/issues/3359#issuecomment-32150214
#===================
ENV TZ "UTC"
RUN echo "${TZ}" > /etc/timezone \
  && dpkg-reconfigure --frontend noninteractive tzdata

#========================================
# Add normal user with passwordless sudo
#========================================
RUN useradd seluser \
         --shell /bin/bash  \
         --create-home \
  && usermod -a -G sudo seluser \
  && echo 'ALL ALL = (ALL) NOPASSWD: ALL' >> /etc/sudoers \
  && echo 'seluser:secret' | chpasswd
ENV HOME=/home/seluser

#=======================================
# Create shared / common bin directory
#=======================================
RUN  mkdir -p /opt/bin 

#======================================
# Add Grid check script
#======================================
COPY check-grid.sh entry_point.sh   /opt/bin/
RUN chmod +x /opt/bin/check-grid.sh

#======================================
# Add Supervisor configuration file
#======================================
COPY supervisord.conf /etc

#==========
# Selenium & relaxing permissions for OpenShift and other non-sudo environments
#==========
RUN  mkdir -p /opt/selenium /var/run/supervisor /var/log/supervisor \
  && touch /opt/selenium/config.json \
  && chmod -R 777 /opt/selenium /var/run/supervisor /var/log/supervisor /etc/passwd \
  && wget --no-verbose https://selenium-release.storage.googleapis.com/3.141/selenium-server-standalone-3.141.59.jar \
    -O /opt/selenium/selenium-server-standalone.jar \
  && chgrp -R 0 /opt/selenium ${HOME} /var/run/supervisor /var/log/supervisor \
  && chmod -R g=u /opt/selenium ${HOME} /var/run/supervisor /var/log/supervisor

#RUN apt-get -qqy update \
# && apt-get -qqy --no-install-recommends install \
#    qemu \
#    qemu-user-static \
#    binfmt-support \
# && cp /usr/bin/qemu-arm-static /usr/bin \
# && sh -c "sed -ie 's/^\([^#].*\)/# \1/g' /etc/ld.so.preload" \
# && update-binfmts --display \
# && rm -rf /var/lib/apt/lists/* /var/cache/apt/*
#===================================================
# Run the following commands as non-privileged user
#===================================================
USER seluser

CMD ["/opt/bin/entry_point.sh"]
