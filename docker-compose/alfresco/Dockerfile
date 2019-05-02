FROM alfresco/alfresco-content-repository:6.1.0

USER root

ARG TOMCAT_DIR=/usr/local/tomcat
ARG ALF_DATA_DIR=${TOMCAT_DIR}/alf_data

COPY keystore  ${ALF_DATA_DIR}/keystore

COPY tomcat/server.xml ${TOMCAT_DIR}/conf/server.xml
COPY tomcat/tomcat-users.xml ${TOMCAT_DIR}/conf/tomcat-users.xml
