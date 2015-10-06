## Please note that we don't use iPython Broker anymore. It was designed to deploy a proxy application to access Docker image of IPython. Now, we use service-exposer to access IPython.

## Get the sources from github

```
git clone git@github.com:trustedanalytics/app-launching-service-broker.git
git checkout DPNG-1510
```

## Prepare catalog.json file

**vim catalog.json**

```
{
  "services": [{
    "id": "111f9a19-a193-4a86-b449-b448deadbeef",
    "name": "ipython-proxy",
    "description": "iPython web console",
    "bindable": true,
    "tags": ["python"],
    "plans": [{
      "id": "2223fc74-8b8d-422b-8217-4a8edeadbeef",
      "name": "simple",
      "description": "simple",
      "free": true
    }]
  }]
}
```

## Prepare node.js proxy application

```
mkdir ./apps/ipythonproxy
cd ./apps/ipythonproxy
vim manifest.yml
```

```
applications:
- name: ipython-proxy
  buildpack: nodejs_buildpack
  memory: 256M
  disk_quota: 256M
  path: .
```

**vim package.json**

```
{
  "name": "iPython-proxy",
  "version": "0.0.1",
  "private": true,
  "engines": {
    "node": "0.10.x"
  },
  "dependencies": {
    "http-proxy": "^1.1.11",
    "colors": "~0.6.2",
    "socket.io": "~0.9.16",
    "socket.io-client": "~0.9.16",
    "connect": "~2.11.0",
    "request": "~2.27.0",
    "connect-restreamer": "~1.0.0"
  }
}
```

**vim server.js**

```
var listen_port = process.env.PORT

var vcap_services  = JSON.parse(process.env.VCAP_SERVICES)

var target_hostname = vcap_services.ipython[0].credentials.hostname
var target_port = vcap_services.ipython[0].credentials.port

console.log("Setting up proxy to " , target_hostname, "  :" , target_port)

var http = require('http'),
    httpProxy = require('http-proxy');


// Create a proxy server with custom application logic

var proxy = httpProxy.createProxyServer({   target: {
    host: target_hostname,
    port: target_port
  },ws: true, web:true});


// Create your custom server and just call `proxy.web()` to proxy
// a web request to the target passed in the options
// also you can use `proxy.ws()` to proxy a websockets request

var server = http.createServer(function(req, res) {
  // You can define here your custom logic to handle the request
  // and then proxy the request.

  proxy.web(req, res);

});

server.on('upgrade', function (req, socket, head) {
  proxy.ws(req, socket, head);
});

console.log("Listening on port " + listen_port)
server.listen(listen_port);
```

The _./apps/ipythonproxy_ folder should have three files:

* server.js - nodejs proxy application
* package.json - app description 
* manifest.yml - cf manifest

Now you can push it into cf.

## Prepare manifest.yml for ipython-broker

Go back to main directory:

```
cd ../../
vim manifest.yml
```

```
applications:
- name: ipython-broker
  memory: 256M
  instances: 1
  buildpack: https://github.com/cloudfoundry/go-buildpack.git#v1.1.2
  path: .
  timeout: 180
  env:
   CF_API: http://your_platform_api_endpoint
   CF_CATALOG_PATH: ./catalog.json
   CF_DEP: ipython|free
   CF_PASS: platform_password 
   CF_SRC: ./apps/ipythonproxy
   CF_USER: platform_username
   APP_NAME_SUFFIX: proxy
```

Take note of the _APP_NAME_SUFFIX_ variable; you will read about it later on.

## Push broker into the platform and enable ipython-proxy service

You need to be in main directory of broker and you can push it with the _cf push_ command. This command will create service broker application. If it is first push of ipython-broker, you will need to create a service broker and enable access to this service (```<service_broker_name>``` should be unique. For example, ipython*, ```<your_service_name>``` should be the same as services -> name in catalog.json or check it using cf service-access):
```
cf create-service-broker ipython-broker admin admin http://<app-url>
cf enable-service-access ipython-proxy
```
If you had ipython-broker before and it is only update you need to run update command:
```
cf update-service-broker ipython-broker admin admin http://<app-url>
```

Now you have ipython-proxy service enabled.

## create ipython-proxy instance from marketplace

Find ipython-proxy in the marketplace and create an instance with the name _foobar_. 

This will create services: 
```
foobar  : useless service, can be removed

foobar-ipython  : docker ipython service instance
```
Application:
```
foobar-proxy : our nodejs proxy application which will point to ipython service (http and secure websockets) 
```
("proxy" is taken from APP_NAME_SUFFIX defined in manifest.yml, if empty, no suffix will be added)

## Connect to ipython

Take the foobar-proxy application address (let's refer to it as **http://foobar-proxy.apps.my_platform.com**) and modify it to allow access by secure websockets:

```
NO MODIFICATION SHOWN IN EXAMPLE CODE.
```

Open this link in the browser:

```
https://foobar-proxy.apps.my_platform.com:4443
```

TODO: some nice ipython screenshots






