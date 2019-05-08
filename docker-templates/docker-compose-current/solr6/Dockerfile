FROM alfresco/alfresco-search-services:1.3.0.1

ARG SOLR_HOME=/opt/alfresco-search-services

USER root

COPY --chown=solr solrcore.properties ${SOLR_HOME}/solrhome/templates/rerank/conf/solrcore.properties

RUN mkdir ${SOLR_HOME}/keystore \
 && chown -R solr:solr ${SOLR_HOME}/keystore

VOLUME ["${SOLR_HOME}/keystore"]

USER solr
