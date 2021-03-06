# Docker services
For development and testing purposes. _Docker services_ is a service broker that allows you to add services easily to Cloud Foundry. 

## Adding a docker image into docker services

In the _Properties_ of a docker image manifest, there is a list of "services" properties. This is used to add the docker image as service.

```yaml
      - id: '617b9186-2e5b-11e4-af5d-a6c5e4d22fb7'
        name: 'rethinkdb'
        description: 'RethinkDB is built to store JSON documents'
        bindable: true
        tags:
          - 'rethinkdb'
          - 'document'
        metadata:
          displayName: 'RethinkDB 1.14.0'
          imageUrl: 'data:image/png;base64,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'
          longDescription: 'RethinkDB is built to store JSON documents'
          providerDisplayName: 'RethinkDB'
          documentationUrl: 'http://rethinkdb.com/'
          supportUrl: 'http://rethinkdb.com/'
        requires: []
        dashboard_client:
          id: 'rethinkdb-dashboard'
          secret: 'rethinkdb-dashboard-secret'
          redirect_uri: (( meta.cfcontainersbroker.external_host "/manage/auth/cloudfoundry/callback" ))
        plans:
          - id: '6194d79a-2e5b-11e4-af5d-a6c5e4d22fb7'
            name: 'free'
            container:
              backend: 'docker'
              image: 'dockerfile/rethinkdb'
              tag: 'latest'
              persistent_volumes:
                - '/data'
            credentials:
              uri:
                prefix: 'rethinkdb'
                port: '28015/tcp'
            description: 'Free Trial'
            metadata:
              costs:
                - amount:
                    usd: 0.0
                  unit: 'MONTHLY'
              bullets:
                - 'Dedicated RethinkDB 1.14.0 server'
                - 'RethinkDB 1.14.0 running inside a Docker container'
```

This is example of a single service. In this case, RethinkDB.

```yaml
      - id: '617b9186-2e5b-11e4-af5d-a6c5e4d22fb7'
        name: 'rethinkdb'
        description: 'RethinkDB is built to store JSON documents'
        bindable: true
        tags:
          - 'rethinkdb'
          - 'document'
```

This setup ID is a UUID that has to be unique across your different services. Name, description, and bindable are required. Tags are used to return back to app for certain tag for service.

```yaml
metadata:
          displayName: 'RethinkDB 1.14.0'
          imageUrl: 'data:image/png;base64,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'
          longDescription: 'RethinkDB is built to store JSON documents'
          providerDisplayName: 'RethinkDB'
          documentationUrl: 'http://rethinkdb.com/'
          supportUrl: 'http://rethinkdb.com/'
```

The _Metadata_ section is used for images, description, and URLs that can be used in a front-end dashboard to display services.

```yaml
        requires: []
        dashboard_client:
          id: 'rethinkdb-dashboard'
          secret: 'rethinkdb-dashboard-secret'
          redirect_uri: (( meta.cfcontainersbroker.external_host "/manage/auth/cloudfoundry/callback" ))
```

The dashboard URL is used if the service needs an oauth client. 


```yaml
plans:
          - id: '6194d79a-2e5b-11e4-af5d-a6c5e4d22fb7'
            name: 'free'
            container:
              backend: 'docker'
              image: 'dockerfile/rethinkdb'
              tag: 'latest'
              persistent_volumes:
                - '/data'
            credentials:
              uri:
                prefix: 'rethinkdb'
                port: '28015/tcp'
            description: 'Free Trial'
            metadata:
              costs:
                - amount:
                    usd: 0.0
                  unit: 'MONTHLY'
              bullets:
                - 'Dedicated RethinkDB 1.14.0 server'
                - 'RethinkDB 1.14.0 running inside a Docker container'
```

Each plan must have a UUID. _Container_ has all the information about the container, such as image, tags, where persistent disk is stored. _Metadata_ is used for billing if needed.

```
            credentials:
              username:
                key: 'POSTGRES_USERNAME'
              password:
                key: 'POSTGRES_PASSWORD'
              dbname:
                key: 'POSTGRES_DBNAME'
              uri:
                prefix: 'postgres'
```

_Credentials_ are environment variables that need to be set for an image to generate credentials. These are generated by the docker services on every new image. These are returned to the app as credentials. The URI prefix is if you want a URI generated as a credential.


When new services are added, the following commands need to be run:

```yaml
cf create-service-broker docker containers containers <broker url>
# List services
cf service-access
cf enable-service-access <service_name>
```