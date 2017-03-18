FROM lsiobase/xenial 
MAINTAINER Educated Warrior

LABEL org.freenas.interactive="false" \
      org.freenas.version="1" \
      org.freenas.upgradeable="true" \
      org.freenas.expose-ports-at-host="true" \
      org.freenas.autostart="true" \
      org.freenas.web-ui-protocol="https" \
      org.freenas.web-ui-port="8443" \
      org.freenas.web-ui-path="" \
      org.freenas.port-mappings="22:22/tcp,8443:8443/tcp,25565:25565/tcp" \
      org.freenas.volumes="[						\
          {								\
              \"name\": \"/var/games/minecraft\",					\
              \"descr\": \"Minecraft Game Folder\"			\
          }								\
      ]" \
     org.freenas.settings="[ 						                        \
          {								                                    \
              \"env\": \"TZ\",					                              \
              \"descr\": \"Timezone information, eg America/New_York\",				\
              \"optional\": true					                              \
          },								                              \
          {								                                    \
              \"env\": \"USER_ID\",					                              \
              \"descr\": \"User ID \",				\
              \"optional\": true					                              \
          },								                              \
          {								                                    \
              \"env\": \"USER_NAME\",					                        \
              \"descr\": \"User Name\",				\
              \"optional\": true					                              \
          },                \
          {								\
              \"env\": \"USER_PASSWORD\",					\
              \"descr\": \"User Password\",				\
              \"optional\": false					\
          }								\	   
      ]" 

#update and accept all prompts
RUN apt-get update && apt-get install -y \
  supervisor \
  rdiff-backup \
  screen \
  openjdk-8-jre-headless \
  rsync \
  git \
  curl \
  rlwrap \
  vim \
  net-tools \
  openssh-server \ 
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#install node from nodesource
RUN curl https://deb.nodesource.com/node_4.x/pool/main/n/nodejs/nodejs_4.6.2-1nodesource1~jessie1_amd64.deb > node.deb \
 && dpkg -i node.deb \
 && rm node.deb

#download mineos from github
RUN mkdir /usr/games/minecraft \
  && cd /usr/games/minecraft \
  && git clone --depth=1 https://github.com/hexparrot/mineos-node.git . \
  && chmod +x webui.js mineos_console.js service.js

#build npm deps and clean up apt for image minimalization
RUN cd /usr/games/minecraft \
  && apt-get update \
  && apt-get install -y build-essential \
  && npm install \
  && apt-get remove --purge -y build-essential \
  && apt-get autoremove -y \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#initialize minecraft
COPY init.sh /init.sh
#RUN cp init.sh /init.sh \
RUN chmod +x /init.sh
RUN /init.sh
#CMD ["sh","init.sh"]

#configure and run supervisor
RUN cp /usr/games/minecraft/init/supervisor_conf /etc/supervisor/conf.d/mineos.conf
CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf"]

# add local files
# COPY root/ /

# ports and volumes
EXPOSE 22 8443 25565-25570
VOLUME /var/games/minecraft  
