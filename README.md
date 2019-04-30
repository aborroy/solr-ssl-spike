# SOLR SSL Configuration

This document describes SOLR SSL Configuration for Alfresco Platform.


# ACS 5.2 and SOLR 4

ACS 5.2 is released by default using Mutual TLS communication with SOLR relying on X509 Certificates.


## Default certificates

Extracted from `alfresco-content-services-distribution-5.2.5.RC2.zip`

Default password for every store and certificate: `kT9X6oe68t`

### Repository

alf_data/keystore/ssl.truststore

```
[PUBL] alfresco.ca
Subject: CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
```

/alf_data/keystore/ssl.keystore

```
[PUBL] ssl.alfresco.ca (unused, alternative alias to "alfresco.ca")
Subject: CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB

[PRIV] ssl.repo
Subject: CN=Alfresco Repository, OU=Unknown, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
```

### SOLR 4

Both *workspace* and *archive* cores are using the same files for the *truststore* and the *keystore*.

**Workspace**

/solr4/workspace-SpacesStore/conf/ssl.repo.client.truststore

```
[PUBL] alfresco.ca
Subject: CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
```

/solr4/workspace-SpacesStore/conf/ssl.repo.client.keystore

```
[PUBL] alfresco.ca (unused)
Subject: CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB

[PRIV] ssl.repo.client
Subject: CN=Alfresco Repository Client, OU=Unknown, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
```

**Archive**

/solr4/archive-SpacesStore/conf/ssl.repo.client.truststore

```
[PUBL] alfresco.ca
Subject: CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB

```

/solr4/archive-SpacesStore/conf/ssl.repo.client.keystore

```
[PUBL] alfresco.ca (unused)
Subject: CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB

[PRIV] ssl.repo.client
Subject: CN=Alfresco Repository Client, OU=Unknown, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
Issuer:  CN=Alfresco CA, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB
```

## Configuration

SSL configuration is performed in Repository side and also in SOLR side.

### Repository

<ALFRESCO_TOMCAT_HOME>/shared/classes/alfresco-global.properties

```
solr.host=localhost
solr.port.ssl=8443
```

<ALFRESCO_TOMCAT_HOME>/conf/server.xml

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
      SSLEnabled="true" maxThreads="150" scheme="https"
      keystoreFile="<ALFRESCO_HOME>/alf_data/keystore/ssl.keystore"
      keystorePass="kT9X6oe68t" keystoreType="JCEKS" secure="true" connectionTimeout="240000"
      truststoreFile="<ALFRESCO_HOME>/alf_data/keystore/ssl.truststore"
      truststorePass="kT9X6oe68t" truststoreType="JCEKS" clientAuth="want" sslProtocol="TLS"/>  
```

<ALFRESCO_TOMCAT_HOME>/conf/tomcat-users.xml

```xml
<user username="CN=Alfresco Repository, OU=Unknown, O=Alfresco Software Ltd., L=Maidenhead, ST=UK, C=GB" roles="repository" password="null"/>
```

/alf_data/keystore/ssl-keystore-passwords.properties

```
aliases=ssl.alfresco.ca,ssl.repo
keystore.password=kT9X6oe68t
ssl.repo.password=kT9X6oe68t
ssl.alfresco.ca.password=kT9X6oe68t
```

/alf_data/keystore/ssl-truststore-passwords.properties

```
aliases=alfresco.ca
keystore.password=kT9X6oe68t
alfresco.ca.password=kT9X6oe68t
```

### SOLR 4

<SOLR4_TOMCAT_HOME>/conf/server.xml

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
      SSLEnabled="true" maxThreads="150" scheme="https"
      keystoreFile="<ALFRESCO_HOME>/alf_data/keystore/ssl.keystore"
      keystorePass="kT9X6oe68t" keystoreType="JCEKS" secure="true" connectionTimeout="240000"
      truststoreFile="<ALFRESCO_HOME>/alf_data/keystore/ssl.truststore"
      truststorePass="kT9X6oe68t" truststoreType="JCEKS" clientAuth="want" sslProtocol="TLS"/>  
```

**Workspace**

/solr4/workspace-SpacesStore/conf/ssl-keystore-passwords.properties

```
aliases=ssl.alfresco.ca,ssl.repo.client
keystore.password=kT9X6oe68t
ssl.repo.client.password=kT9X6oe68t
ssl.alfresco.ca.password=kT9X6oe68t
```

/solr4/workspace-SpacesStore/conf/ssl-truststore-passwords.properties

```
aliases=alfresco.ca
keystore.password=kT9X6oe68t
alfresco.ca.password=kT9X6oe68t
```

/solr4/workspace-SpacesStore/conf/solrcore.properties

```
alfresco.host=localhost
alfresco.port.ssl=8443

alfresco.encryption.ssl.keystore.type=JCEKS
alfresco.encryption.ssl.keystore.provider=
alfresco.encryption.ssl.keystore.location=ssl.repo.client.keystore
alfresco.encryption.ssl.keystore.passwordFileLocation=ssl-keystore-passwords.properties
alfresco.encryption.ssl.truststore.type=JCEKS
alfresco.encryption.ssl.truststore.provider=
alfresco.encryption.ssl.truststore.location=ssl.repo.client.truststore
alfresco.encryption.ssl.truststore.passwordFileLocation=ssl-truststore-passwords.properties
```

**Archive**

/solr4/archive-SpacesStore/conf/ssl-keystore-passwords.properties

```
aliases=ssl.alfresco.ca,ssl.repo.client
keystore.password=kT9X6oe68t
ssl.repo.client.password=kT9X6oe68t
ssl.alfresco.ca.password=kT9X6oe68t
```

/solr4/archive-SpacesStore/conf/ssl-truststore-passwords.properties

```
aliases=alfresco.ca
keystore.password=kT9X6oe68t
alfresco.ca.password=kT9X6oe68t
```

/solr4/archive-SpacesStore/conf/solrcore.properties

```
alfresco.host=localhost
alfresco.port.ssl=8443

alfresco.encryption.ssl.keystore.type=JCEKS
alfresco.encryption.ssl.keystore.provider=
alfresco.encryption.ssl.keystore.location=ssl.repo.client.keystore
alfresco.encryption.ssl.keystore.passwordFileLocation=ssl-keystore-passwords.properties
alfresco.encryption.ssl.truststore.type=JCEKS
alfresco.encryption.ssl.truststore.provider=
alfresco.encryption.ssl.truststore.location=ssl.repo.client.truststore
alfresco.encryption.ssl.truststore.passwordFileLocation=ssl-truststore-passwords.properties
```

## Communication

### Tracking from SOLR

When SOLR starts, every tracker is registered by using `solrcore.properties` settings.

![SOLR Tracker registration process](solr-tracker-register.png)

Trackers are targeting different Repository endpoints:

* Transactions: "api/solr/transactions"
* Metadata: "api/solr/metadata"
* Nodes: "api/solr/nodes"
* Content: "api/solr/textContent"

Repository endpoints are developed as WebScrips without authentication

[nodesMetaData.post.desc.xml](https://github.com/Alfresco/alfresco-remote-api/blob/master/src/main/resources/alfresco/templates/webscripts/org/alfresco/repository/solr/nodesMetaData.post.desc.xml)

```xml
<webscript>
  <shortname>Get the metadata for the specified nodes</shortname>
  <description>Get the metadata for the specified nodes.</description>
  <url>/api/solr/metadata</url>
  <format default="json">argument</format>
  <!-- Solr api is protected by a solr-specific authentication mechanism -->
  <authentication>none</authentication>
  <transaction allow="readonly">required</transaction>
  <lifecycle>internal</lifecycle>
  <family>SOLR</family>
</webscript>
```

When a tracker, *MetadataTracker* in the diagram, invokes the repository, Tomcat Connector is performing TLS Mutual Auth operations and also user permissions checking in Tomcat Manager component.

![SOLR Tracker Invocation](solr-tracker-invocation.png)

Alfresco X509Filter is just setting the request as secure for administration purposes.


### Querying from Alfresco

When Alfresco performs a query on an SSL SOLR, Tomcat Connector is performing TLS Mutual Auth operations, but there is no user permissions checking.

![Alfresco Search Invocation](alfresco-query.png)


# SOLR 6

In order to have the same configuration running with SOLR 6, enabling SSL in Jetty is required to provide querying from Alfresco.

https://lucene.apache.org/solr/guide/6_6/enabling-ssl.html

However, tracking operations should work as it worked changing only default configuration.
