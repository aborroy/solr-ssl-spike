# Sample configuration for custom SSL Certificates

These project describes how to configure Alfresco and SOLR to use SSL/TLS Communication with mutual authentication when using custom certificates.

Also an *SSL Tool* is provided in order to generated required *truststores*, *keystores* and *certificates*.

## Components

* **alfresco** includes a `Dockerfile` with *Tomcat Connector* configuration and *Keystore* folder mapping. Encryption `keystore` files (keystore, keystore-passwords.properties) are included from current sources code, as it's not required for this configuration but it's required for Repository to work.

* **docker-compose.yml** includes a Docker Composition for latest Alfresco Repository and Internal Search Services (https://git.alfresco.com/search_discovery/searchservicesinternal)

* **keystore** folder is empty by default, it's used as a Docker VOLUME to provide truststores and keystores to SOLR Container

* **solr6** includes a `Dockerfile` with SOLR Configuration to include customised store names and passwords (solr.in.sh)

* **ssl-tool** includes a Shell Script (run.sh) to generate truststores, keystores and certificates. Also a default OpenSSL CA configuration is provided (openssl.conf).

```
.
├── alfresco
│   ├── Dockerfile
│   └── keystore
│       ├── keystore
│       └── keystore-passwords.properties
├── docker-compose.yml
├── keystore
│   └── empty
├── solr6
│   ├── Dockerfile
│   └── solr.in.sh
└── ssl-tool
    ├── openssl.cnf
    └── run.sh
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

## Generating stores and certificates

SSL Tool folder includes generation Script for custom certificates.

```bash
$ cd ssl-tool

$ ./run.sh

$ tree keystores
keystores
├── alfresco
│   ├── ssl-keystore-passwords.properties
│   ├── ssl-truststore-passwords.properties
│   ├── ssl.keystore
│   └── ssl.truststore
├── client
│   └── browser.p12
└── solr
    ├── ssl-keystore-passwords.properties
    ├── ssl-truststore-passwords.properties
    ├── ssl.repo.client.keystore
    └── ssl.repo.client.truststore
```

## Copying certificates to deployment directories

Once the stores and certificates have been generated, they must be copied to deployment folder in Docker Compose.

```bash
$ cp keystores/alfresco/* ../alfresco/keystore/
$ cp keystores/solr/* ../keystore/
```

## Running Docker Compose

When everything in place, Docker can be started.

```bash
$ cd ..
$ docker-compose up --build
```

And alfresco will be available to test at:

http://localhost:8080/alfresco

http://localhost:8080/share

https://localhost:8983/solr

SSL Communication from SOLR will be targeted to http://alfresco:8443/alfresco
