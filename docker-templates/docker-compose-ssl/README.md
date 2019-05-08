# Default configuration for SSL/TLS Communication

These project describes a default configuration for Alfresco and SOLR to use SSL/TLS Communication with mutual authentication when using default certificates.

Every *truststores*, *keystores* and *certificates* is copied from sources.

## Components

* **alfresco** includes a `Dockerfile` with *Tomcat Connector* configuration and *Keystore* folder mapping as it's required for Connector. Default stores and certificates from source code (`alfresco-repository`) have been copied in this folder.

* **docker-compose.yml** includes a Docker Composition for latest Alfresco Repository and Internal Search Services (https://git.alfresco.com/search_discovery/searchservicesinternal)

* **keystore** includes default certificates for SOLR server copied from source code (`alfresco-search`)

```
├── alfresco
│   ├── Dockerfile
│   └── keystore
│       ├── keystore
│       ├── keystore-passwords.properties
│       ├── ssl-keystore-passwords.properties
│       ├── ssl-truststore-passwords.properties
│       ├── ssl.keystore
│       └── ssl.truststore
└── docker-compose.yml
```

Alfresco repository is configured to use SSL/TLS with SOLR.

```
alfresco:
    image: alfresco/alfresco-content-repository:latest
    environment:
        JAVA_OPTS: "
            -Dsolr.secureComms=https
```

SOLR is configured to use plain SSL/TLS with Alfresco.

```
solr6:
    image: searchservices:develop
    mem_limit: 2g
    environment:
        SOLR_ALFRESCO_HOST: "alfresco"
        SOLR_ALFRESCO_PORT: "8443"
        SOLR_USE_SSL: "true"
        SOLR_OPTS: "
            -Dsolr.ssl.checkPeerName=false
        "
```

## Building Search Services Docker Image

This project is using an *internal* Search Services Docker Image (currently living at `fix/MNT-20593_SSLByDefault` branch), so it's required to build it before starting.

```bash
$ git clone git@git.alfresco.com:search_discovery/searchservicesinternal.git

$ cd searchservicesinternal
$ git checkout fix/MNT-20593_SSLByDefault

$ mvn clean install -DskipTests=true

$ cd packaging/target/docker-resources/
$ docker build -t searchservices:develop .
```

## Running Docker Compose

When everything in place, Docker can be started.

```bash
$ docker-compose up --build
```

And alfresco will be available to test at:

http://localhost:8080/alfresco

http://localhost:8080/share

https://localhost:8983/solr

SSL Communication from SOLR will be targeted to http://alfresco:8443/alfresco
