FROM armhf/ubuntu:14.04

# No interactive frontend during docker build
ENV DEBIAN_FRONTEND=noninteractive \
    DEBCONF_NONINTERACTIVE_SEEN=true

# Install dependencies
RUN apt-get update && apt-get install -y \
    wget \
    unzip \
    tzdata \
    sudo \
    software-properties-common \
    ca-certificates \
  && add-apt-repository ppa:openjdk-r/ppa \
  && apt-get update \
  && apt-get install openjdk-8-jdk -y \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

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

#===================================================
# Run the following commands as non-privileged user
#===================================================
USER seluser

#==========
# Selenium
#==========
RUN sudo mkdir -p /opt/selenium \
  && sudo chown seluser:seluser /opt/selenium \
  && wget --no-verbose http://selenium-release.storage.googleapis.com/3.4/selenium-server-standalone-3.4.0.jar -O /opt/selenium/selenium-server-standalone.jar \
  && chmod 744 /opt/selenium/selenium-server-standalone.jar

