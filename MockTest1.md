1) Deploy a pod named nginx-pod using the nginx:alpine image

controlplane ~ ➜  alias kr="k run"

controlplane ~ ➜  kr nginx-pod --image=nginx:alpine
pod/nginx-pod created

controlplane ~ ➜  k get pods
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          5s


2) Deploy a messaging pod using the redis:alpine image with the labels set to tier=msg.

controlplane ~ ➜  kr messaging --image=redis:alpine --labels="tier=msg"
pod/messaging created

controlplane ~ ➜  k get pods --show-labels 
NAME        READY   STATUS    RESTARTS   AGE    LABELS
messaging   1/1     Running   0          9s     tier=msg
nginx-pod   1/1     Running   0          116s   run=nginx-pod


3) 

controlplane ~ ➜  k create ns apx-x9984574
namespace/apx-x9984574 created

4)

controlplane ~ ➜  k get pods
NAME        READY   STATUS    RESTARTS   AGE
messaging   1/1     Running   0          113s
nginx-pod   1/1     Running   0          3m40s

controlplane ~ ➜  k expose pod messaging --name=messaging-service --port=6379 --type=ClusterIP
service/messaging-service exposed

controlplane ~ ➜  k get services
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
kubernetes          ClusterIP   10.96.0.1        <none>        443/TCP    87m
messaging-service   ClusterIP   10.102.228.243   <none>        6379/TCP   4s

controlplane ~ ➜  k get ep
NAME                ENDPOINTS           AGE
kubernetes          192.38.114.6:6443   87m
messaging-service   10.244.0.5:6379     7s


5)


controlplane ~ ➜  k create deployment hr-web-app --image kodekloud/webapp-color --replicas 2
deployment.apps/hr-web-app created

controlplane ~ ➜  k get pods
NAME                          READY   STATUS              RESTARTS   AGE
hr-web-app-5d6b77db78-wqskm   0/1     ContainerCreating   0          3s
hr-web-app-5d6b77db78-zqqr9   0/1     ContainerCreating   0          3s
messaging                     1/1     Running             0          3m17s
nginx-pod                     1/1     Running             0          5m4s

6) 

kubectl run hr-web-app --image kodekloud/webapp-color --replicas=2


7)

k run static-busybox --image=busybox -o yaml --dry-run=client > static.yaml

> Add sleep inside the yaml file

controlplane /etc/kubernetes/manifests ✖ k get pods -o wide
NAME                          READY   STATUS      RESTARTS      AGE     IP           NODE           NOMINATED NODE   READINESS GATES
hr-web-app-5d6b77db78-wqskm   1/1     Running     0             3m15s   10.244.0.6   controlplane   <none>           <none>
hr-web-app-5d6b77db78-zqqr9   1/1     Running     0             3m15s   10.244.0.7   controlplane   <none>           <none>
messaging                     1/1     Running     0             6m29s   10.244.0.5   controlplane   <none>           <none>
nginx-pod                     1/1     Running     0             8m16s   10.244.0.4   controlplane   <none>           <none>
static-busybox-controlplane   0/1     Completed   2 (34s ago)   36s     10.244.0.8   controlplane   <none>           <none>

8)


kubectl run temp-bus --image=redis:alpine --namespace=finance

controlplane /etc/kubernetes/manifests ➜  k get pods -n temp-bus
NAME      READY   STATUS    RESTARTS   AGE
finance   1/1     Running   0          12s


9)

- sleeeep 2;typo:

controlplane /etc/kubernetes/manifests ➜  k get pods
NAME                          READY   STATUS    RESTARTS   AGE
hr-web-app-5d6b77db78-wqskm   1/1     Running   0          7m50s
hr-web-app-5d6b77db78-zqqr9   1/1     Running   0          7m50s
messaging                     1/1     Running   0          11m
nginx-pod                     1/1     Running   0          12m
orange                        1/1     Running   0          5s
static-busybox-controlplane   1/1     Running   0          4m33s


10)

apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: hr-web-app
  name: hr-web-app-service
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
    nodePort: 30082
  selector:
    app: hr-web-app
  type: NodePort
status:
  loadBalancer: {}

11)
controlplane /etc/kubernetes/manifests ➜  k get nodes -o json | jq ".items[].status.nodeInfo.osImage"
"Ubuntu 22.04.5 LTS"

12)

  GNU nano 6.2                                              pv.yaml                                                       
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/pv/data-analytics"



Your score
100%
Pass Percentage - 66%
