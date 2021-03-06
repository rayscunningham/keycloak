# Keycloak Kubernetes

Example Docker file for clustered Keycloak using Kubernetes

## Environment Variables

### POD_NAMESPACE

Uses 'default' kubernetes namespace by default. Config for KUBE_PING.

### POD_IP

See config YAML below.  Uses the Kubernetes Pod IP address for node-identifier forwill remove warning "WFLYTX0013: The node-identifier attribute on the /subsystem=transactions is set to the default value. This is a danger for environments running multiple servers. Please make sure the attribute value is unique."

### CACHE_OWNERS

Uses '1' as default.  See http://www.keycloak.org/docs/latest/server_installation/index.html#replication-and-failover

### PROXY_ADDRESS_FORWARDING

Uses 'false' as default.  See http://www.keycloak.org/docs/latest/server_installation/index.html#_setting-up-a-load-balancer-or-proxy

## Kubernetes config YAML

### Expose ports

    spec:
      containers:
        env:
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        ports:
        - containerPort: 8443
          protocol: TCP
        - containerPort: 8080
          protocol: TCP
        - containerPort: 8888
          protocol: TCP
        - containerPort: 7600
          protocol: TCP

WFLYCTL0419: 'keycloak-4257859680-4wkcm' is an invalid value for parameter node-identifier. Values must have a maximum length of 23 bytes"
### PostgreSQL

    spec:
      containers:
        env:
        - name: DB_VENDOR
          value: POSTGRES
        - name: POSTGRES_PORT_5432_TCP_ADDR
          value: x.x.x.x
        - name: POSTGRES_USER
          value: keycloak
        - name: POSTGRES_PASSWORD
          value: password   

### MySQL

    spec:
      containers:
        env:
        - name: DB_VENDOR
          value: MYSQL
        - name: MYSQL_DATABASE
          value: keycloak
        - name: MYSQL_USER
          value: keycloak
        - name: MYSQL_PASSWORD
          value: password   

## Example logs to verify that KUBE_PING is enabled and working 

    keycloak	2017-12-06T15:02:01.680817449Z	[0m[0m15:02:01,680 INFO [org.jgroups.protocols.kubernetes.KUBE_PING] (ServerService Thread Pool -- 50) namespace [default] set; clustering enabled

    keycloak	2017-12-06T15:02:01.890221143Z	[0m[0m15:02:01,889 INFO [org.jgroups.protocols.kubernetes.KUBE_PING] (ServerService Thread Pool -- 50) Starting UndertowServer on port 8888 for channel address: keycloak-xxxxxxxxxx-p1dt2

    keycloak	2017-12-06T15:02:01.903287574Z	[0m[0m15:02:01,902 INFO [org.jgroups.protocols.kubernetes.KUBE_PING] (ServerService Thread Pool -- 50) UndertowServer started.

    keycloak	2017-12-06T15:02:05.364255292Z	[0m[0m15:02:05,363 INFO [org.infinispan.remoting.transport.jgroups.JGroupsTransport] (MSC service thread 1-1) ISPN000079: Channel ejb local address is keycloak-xxxxxxxxxx-p1dt2, physical addresses are [x.x.x.x:7600]

    keycloak	2017-12-06T15:02:05.360684493Z	[0m[0m15:02:05,360 INFO [org.infinispan.remoting.transport.jgroups.JGroupsTransport] (MSC service thread 1-1) ISPN000094: Received new cluster view for channel ejb: [keycloak-xxxxxxxxxx-jp28w|2] (3) [keycloak-xxxxxxxxxx-jp28w, keycloak-xxxxxxxxxx-05726, keycloak-xxxxxxxxxx-p1dt2]

    keycloak	2017-12-06T15:01:45.248596958Z	[0m[KEYCLOAK DOCKER IMAGE] Using JGroups kubernetes

## Other details

This image extends the [`jboss/keycloak`](https://github.com/jboss-dockerfiles/keycloak) image. Please refer to the README.md for selected images for more info.
