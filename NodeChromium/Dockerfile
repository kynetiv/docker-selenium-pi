FROM kynetiv/selenium-node-base-pi:3.4.0

USER root

#============================================
# Chromium Broswer and Chromium Chrome Driver
#============================================
RUN apt-get update -qqy \
  && apt-get -qqy install \
     chromium-browser \
     chromium-chromedriver \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

USER seluser

# linking it to where chromedriver would be avoids setting custom binary paths
RUN sudo ln -fs /usr/lib/chromium-browser/chromedriver /usr/bin/chromedriver

COPY generate_config /opt/bin/generate_config

#=================================
# Chrome Launch Script Modification
#=================================

COPY chrome_launcher.sh /opt/google/chrome/google-chrome
