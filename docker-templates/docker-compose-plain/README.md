# Default configuration for SSL/TLS Communication

These project describes a default configuration for Alfresco and SOLR to use Plain HTTP Communication.

## Components

* **docker-compose.yml** includes a Docker Composition for latest Alfresco Repository and Internal Search Services (https://git.alfresco.com/search_discovery/searchservicesinternal)

```
└── docker-compose.yml
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

http://localhost:8983/solr

## Recommendations

When running this configuration, access to SOLR APIs and SOLR Web Console must be performed in HTTP Proxy.

Sample for nginx

```
# Protect access to SOLR APIs
location ~ ^(/.*/service/api/solr/.*)$ {return 403;}
location ~ ^(/.*/s/api/solr/.*)$ {return 403;}
location ~ ^(/.*/wcservice/api/solr/.*)$ {return 403;}
location ~ ^(/.*/wcs/api/solr/.*)$ {return 403;}

location ~ ^(/.*/proxy/alfresco/api/solr/.*)$ {return 403 ;}
location ~ ^(/.*/-default-/proxy/alfresco/api/.*)$ {return 403;}

# SOLR Web Console
location /solr/ {
    proxy_pass http://solr6:8983;

    # Basic authentication
    auth_basic "Solr web console";
    auth_basic_user_file /etc/nginx/conf.d/nginx.htpasswd;

    proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
    proxy_redirect off;
    proxy_buffering off;
    proxy_set_header Host            $host;
    proxy_set_header X-Real-IP       $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass_header Set-Cookie;
} 
```
