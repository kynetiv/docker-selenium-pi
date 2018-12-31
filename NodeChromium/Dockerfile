FROM kynetiv/selenium-node-base-pi:3.141.59-dubnium

USER root

#============================================
# Chromium Broswer and Chromium Chrome Driver
#============================================
RUN apt-get update -qqy \
  && apt-get -qqy install \
     chromium-browser \
     chromium-chromedriver \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#=================================
# Chrome Launch Script Wrapper
#=================================
COPY wrap_chrome_binary /opt/bin/wrap_chrome_binary
RUN /opt/bin/wrap_chrome_binary

USER seluser

#============================================
# Chrome webdriver
#============================================
# linking chromium-chromedriver to where chromedriver would be in path
# this avoids needing to set custom binary paths
RUN sudo ln -fs /usr/lib/chromium-browser/chromedriver /usr/bin/chromedriver


# We install the available chromium chromedriver above with browser
# commenting out for reference
#============================================
#ARG CHROME_DRIVER_VERSION="latest"
#RUN CD_VERSION=$(if [ ${CHROME_DRIVER_VERSION:-latest} = "latest" ]; then echo $(wget -qO- https://chromedriver.storage.googleapis.com/LATEST_RELEASE); else echo $CHROME_DRIVER_VERSION; fi) \
#  && echo "Using chromedriver version: "$CD_VERSION \
#  && wget --no-verbose -O /tmp/chromedriver_linux64.zip https://chromedriver.storage.googleapis.com/$CD_VERSION/chromedriver_linux64.zip \
#  && rm -rf /opt/selenium/chromedriver \
#  && unzip /tmp/chromedriver_linux64.zip -d /opt/selenium \
#  && rm /tmp/chromedriver_linux64.zip \
#  && mv /opt/selenium/chromedriver /opt/selenium/chromedriver-$CD_VERSION \
#  && chmod 755 /opt/selenium/chromedriver-$CD_VERSION \
#  && sudo ln -fs /opt/selenium/chromedriver-$CD_VERSION /usr/bin/chromedriver

COPY generate_config /opt/bin/generate_config

# Generating a default config during build time
RUN /opt/bin/generate_config > /opt/selenium/config.json
