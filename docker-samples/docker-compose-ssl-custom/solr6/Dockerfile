FROM searchservices:develop

ARG SOLR_HOME=/opt/alfresco-search-services

USER root

# Included as external file to provide custom passwords and other configurations
COPY --chown=solr solr.in.sh ${SOLR_HOME}/solr.in.sh

USER solr
