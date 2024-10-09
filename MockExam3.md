1.


    2  k create serviceaccount pvviewer
    4  k create clusterrole pvviewer-role --resource=persistentvolumes --verb=list
    9  k create clusterrolebinding pvviewer-role-binding --clusterrole pvviewer-role --serviceaccount=default:pvviewer
   14  k apply -f z.yaml 
   15  history


apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pvviewer
  name: pvviewer
spec:
  serviceAccountName: pvviewer
  containers:
  - image: redis
    name: pvviewer
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

2.

 kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}' > /root/CKA/node_ips

3.

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi-pod
  name: multi-pod
spec:
  containers:
  - image: nginx
    name: alpha
    env:
      - name: name
        value: alpha
    resources: {}
  - image: busybox
    name: beta
    command: ["sleep", "4800"]
    resources: {}
    env:
      - name: name
        value: beta
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}


4.

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: non-root-pod
  name: non-root-pod
spec:
  securityContext:
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - image: redis:alpine
    name: non-root-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}


5.

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
  namespace: default
spec:
  podSelector:
    matchLabels:
      run: np-test-1
  policyTypes:
  - Ingress
  ingress:

    ports:
    - protocol: TCP
      port: 80


6.

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: prod-redis
  name: prod-redis
spec:
  tolerations:
  - key: "env_type"
    operator: "Equal"
    value: "production"
    effect: "NoSchedule"
  containers:
  - image: redis:alpine
    name: dev-redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

controlplane ~ ➜  k run dev-redis --image=redis:alpine
pod/dev-redis created

controlplane ~ ➜  k get pods -o wide
NAME           READY   STATUS              RESTARTS   AGE     IP             NODE           NOMINATED NODE   READINESS GATES
dev-redis      0/1     ContainerCreating   0          5s      <none>         controlplane   <none>           <none>
multi-pod      2/2     Running             0          8m51s   10.244.192.2   node01         <none>           <none>
non-root-pod   1/1     Running             0          7m18s   10.244.192.3   node01         <none>           <none>
np-test-1      1/1     Running             0          7m13s   10.244.192.4   node01         <none>           <none>
pvviewer       1/1     Running             0          18m     10.244.192.1   node01         <none>           <none>

controlplane ~ ➜  nano t.yaml 

controlplane ~ ➜  kubectl apply -f t.yaml 
pod/prod-redis created

controlplane ~ ➜  k get pods -o wide
NAME           READY   STATUS    RESTARTS   AGE     IP             NODE           NOMINATED NODE   READINESS GATES
dev-redis      1/1     Running   0          39s     10.244.0.4     controlplane   <none>           <none>
multi-pod      2/2     Running   0          9m25s   10.244.192.2   node01         <none>           <none>
non-root-pod   1/1     Running   0          7m52s   10.244.192.3   node01         <none>           <none>
np-test-1      1/1     Running   0          7m47s   10.244.192.4   node01         <none>           <none>
prod-redis     1/1     Running   0          3s      10.244.192.5   node01         <none>           <none>
pvviewer       1/1     Running   0          19m     10.244.192.1   node01         <none>           <none>



7.

controlplane ~ ➜  k create ns hr
namespace/hr created

controlplane ~ ➜  k run hr-pod -n hr --image=redis:alpine --labels="environment=production,tier=frontend"
pod/hr-pod created

controlplane ~ ➜  k get pods --show-labels 
NAME           READY   STATUS    RESTARTS   AGE     LABELS
dev-redis      1/1     Running   0          2m39s   run=dev-redis
multi-pod      2/2     Running   0          11m     run=multi-pod
non-root-pod   1/1     Running   0          9m52s   run=non-root-pod
np-test-1      1/1     Running   0          9m47s   run=np-test-1
prod-redis     1/1     Running   0          2m3s    run=prod-redis
pvviewer       1/1     Running   0          21m     run=pvviewer



8.

Incorrect Port - change to 6443

9.

Typo on image, command and controller name


