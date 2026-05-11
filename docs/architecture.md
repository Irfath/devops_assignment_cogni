
# Architecture

1. **Gateway -**

    Primary Ingress Point. 
    this routes external requests to redis cache. 

**2. Pinger -**
    Health check monitor. 

    URL : http://gateway.assessment.svc.cluster.local/healthz

**3. Redis Cache - **

   Pinger health checks and Status codes periodically. 

**4. Docker Compose**
    Services uses the default network, 

**5. Kubernetes**

    ClusterIP as Loadbalancer, 

    FQDN : http://gateway.assessment.svc.cluster.local:80/healthz. - Namespace base

    Namespace           : assessment
    Service Entry Port  : 8000
    Target Port         : 80

** @@@ Issue Fixes : **

**1. Network Port Issue. **

- PINGER_HOST=pinger-svc
Changed to the Correct service name, For correct ervice name connectivity. 

**2. 
containers:**
        - name: gateway
          image: devops/gateway:latest
          ports:
            - containerPort: 8000  # port change to 8000 from 8080

    Gateway connectivity port is 8000 .

**3. 
LivenessProbe**

Liveness probe is responsible for to check if the container is Dead or Alive. 
If the liveness prob fails tit is responsible for to kill the container. 

Pods were pushed into "CrashLoopBackOff" 
Pods are getting killed coz the port was incorrect here. the New container is always killed. 

Change

livenessProbe:
             httpGet:
               path: /healthz
-              port: 8080
+              port: 8000 # port change to 8000 from 8080

**ReadinessProbes**

 Readiness Probe determines if a container is ready to serve traffic.
 It checks if the application has finished its startup.


 readinessProbe:
             httpGet:
               path: /readyz
-              port: 8080
+              port: 8000 # port change to 8000 from 8080

readinessProbe port was incorrect here. Changed to 8000 for correct connectivity. 


**4. Gateway Service**

selector:
-    app: gw
+    app: gateway # selector changed

Select was incorrect here. 

Pods were labeled as "gateway" and gatewat service slector was incorret. 

>> kubectl get endpoints gateway -n assessment

Endpoint was not identified. 


**5. resource list**

Redis deployements was not automatically deployed.

kustomization.yaml must have the redis resorce to initite the correct. 
To create the Redis Deployment and the Redis Service.



**6. Error**

@@ no such host or NXDOMAIN.

in the pinger was unable to find the correct host due to Incorrect FDQN

  TARGET_PROTO: "http"
-  TARGET_HOST: "gateway.default.svc.cluster.local"
-  TARGET_PORT: "8000"
+  TARGET_HOST: "gateway.assessment.svc.cluster.local" # namespace changed to default from assessment
+  TARGET_PORT: "80" # port change to 80 from 8000 svc not accisble

Fixed the FDQN Namespace and the target port. 


**7. Persistent Volume Issue**

There was an even in the log was unable to pick the Persistent Volume. 

Event Error
""""persistentvolumeclaim "redis-data" not found"""""

**8. Redis Port Issue**

Redis was unable to connect,

Error Got :
Failed to connect to redis: dial tcp 10.96.x.x:6379: connect: connection refused.

Redis default port is 6379 was fixed in the redis/servivce.yaml


**9. Structural error in K8**


>>kubectl apply -k k8s/overlays/dev

got the error 
error: resource apps/v1, Kind=Deployment ... does not match patch Kind=DaemonSet

DaemonSet is supposed to run a copy of the pod each noded. Mostly we are using this for monitoring pods. 

Deployments:
    Runs a replica number of pods. 

Fix : 
/k8s/overlays/dev/patches/resource-limits.yaml

 apiVersion: apps/v1
-kind: DaemonSet
+kind: Deployment
 metadata:


 **10. ImagePullBackOff**

 Usully this haapend when the imagePullpolicy is not defined, 

imagePullPolicy: IfNotPresent
    addding this fied the imagepullbackoff Error. 

b/k8s/overlays/staging/patches/resource-limits.yaml

 #imagePullPolicy: IfNotPresent # added check for error


**ImagePullPolicy**

For this i have tagged and pushed the :latest image. 

>docker build -t devops/gateway:latest ./gateway
>docker build -t devops/pinger:latest ./pinger
>kind load docker-image devops/gateway:latest
>kind load docker-image devops/pinger:latest


## Service Communication

The gateway service acts as the entry point. It routes requests to the pinger service and uses Redis for caching.

The pinger service periodically checks the health of the gateway and stores results.

Redis is used as a shared cache between services.

## Configuration

All services are configured via environment variables. See each service's source code for available options.

## Networking

In Docker Compose, services communicate via the Docker network using service names as hostnames.

In Kubernetes, services communicate via ClusterIP services and DNS (e.g., `<service>.<namespace>.svc.cluster.local`).






# All the Changes ive made

1. 
