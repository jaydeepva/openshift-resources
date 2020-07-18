# Introduction
OpenShift provides internal CA that can issue certificates for your use to use mTLS authentication between services and encrypt data in transit.

Let us take an example of service-A which communicates with service-B. In this case service-B acts as server and service-A is client.

## Step 1 - Annotate your service
You need to annotate service-B so that OpenShift issue certificates that can be used by service-B when start up. An example is given below. This annotation denotes that the server certificates will be available in secret name `serviceb-certs`.

```
apiVersion: v1
kind: Service
metadata:
  name: 'my-service-b'
  namespace: '{{ meta.namespace }}'
  labels:
    app: my-serviceb
  annotations:
    service.beta.openshift.io/serving-cert-secret-name: serviceb-certs
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      name: serviceb
  selector:
	...
```

## Step 2 - Mount the secret in your container
You can mount the secret in your container. The secret will provide tls.key and tls.crt

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: 'my-service-b'
  namespace: '{{ meta.namespace }}'
  labels:
    app: my-serviceb
spec:
  replicas: 2
  template:
    spec:
      volumes:
      - name: server-certs-secret
        secret:
          secretName: 'serviceb-certs'
      containers:
      - name: my-serviceb-pod
        image: my-image-repo/my-image:my-image-tag
        volumeMounts:
          - mountPath: /etc/serviceb/server-certs
            name: server-certs-secret
            readOnly: true
```

## Step 3 - Use certificate key and certificate with your server
Here is an example of node.js code that uses the certificate and key while starting server

```
var https = require('https')
var app = require('express')();
var port = 80;

var CERTS_FILE = '/etc/serviceb/server-certs/tls.crt';
var CERTS_KEY = '/etc/serviceb/server-certs/tls.key';

https.createServer({
	key: fs.readFileSync(path.resolve(__dirname, CERTS_KEY)),
	cert: fs.readFileSync(path.resolve(__dirname, CERTS_FILE))
}, app).listen(port, function() {
	console.log("server started...");
});
```

## Step 4 - Create a configMap to get client certificate
You need to create a configMap and annotate that so that OpenShift can put client certificate into the configMap. The client certificate name is service-ca.crt

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: 'client-certs'
  namespace: '{{ meta.namespace }}'
  annotations:
    service.beta.openshift.io/inject-cabundle: 'true'
data:
  desc: this configmap will hold client certs for ssl 
```

## Step 5 - Mount the configMap in your client container
You need to map the configMap in your client container which is service-A so that service-A gets access to client certificate. Here is an example

```
kind: Deployment
apiVersion: apps/v1
metadata:
  name: 'my-service-a'
  namespace: '{{ meta.namespace }}'
spec:
  selector:
    matchLabels:
      app: service-a
  template:
    metadata:
      labels:
        app: service-a
    spec:
      volumes:
      - name: client-certs-config
        configMap:
          name: 'client-certs'
      containers:
      - name: service-a-pod
        image: my-image-repo/my-image-name:my-image-tag
        volumeMounts:
          - mountPath: /etc/servicea/client-certs
            name: client-certs-config
            readOnly: true
```

## Step 6 - Use client cert to connect to service B
In your service-A, now use the client cert to connect to service-B. If you are using node.js, here is an example

```
const fs = require('fs');
const request = require('request');

let myCaFile = fs.readFileSync('/etc/servicea/client-certs/service-ca.crt')
 
var options = {
    url: 'https://<service-B-url>',
	json: true,
    ca: myCaFile
};

request.get(options);
```
