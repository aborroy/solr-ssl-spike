FROM alfresco/alfresco-search-services:1.3.0.1

ARG SOLR_HOME=/opt/alfresco-search-services

USER root

RUN mkdir ${SOLR_HOME}/keystore
COPY solrcore.properties ${SOLR_HOME}/solrhome/templates/rerank/conf/solrcore.properties

RUN chown -R solr:solr ${SOLR_HOME}/keystore
RUN chown -R solr:solr ${SOLR_HOME}/solrhome/templates/rerank/conf/solrcore.properties

USER solr

VOLUME ["${SOLR_HOME}/keystore"]
