#verdaccio v5
FROM verdaccio/verdaccio:5
ADD config.yaml /verdaccio/conf/config.yaml  
USER root
RUN npm install verdaccio-s3-storage -g
RUN npm install verdaccio-ldap -g
USER $VERDACCIO_USER_UID
