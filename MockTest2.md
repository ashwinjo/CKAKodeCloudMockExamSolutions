1. 

controlplane ~ ➜  ETCDCTL_API=3 etcdctl snapshot save /opt/etcd-backup.db --endpoints=https://127.0
.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key
=/etc/kubernetes/pki/etcd/server.key
Snapshot saved at /opt/etcd-backup.db


2. 

apiVersion: v1
kind: Pod
metadata:
  name: redis-storage
spec:
  containers:
  - image: redis:alpine
    name: redis-storage
    volumeMounts:
    - mountPath: /data/redis
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir:
      sizeLimit: 500Mi

3.

  GNU nano 6.2                                   a.yaml                                            
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: super-user-pod
  name: super-user-pod
spec:
  containers:
  - image: busybox:1.28
    name: super-user-pod
    resources: {}
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

4.

First create pvc to bind

  GNU nano 6.2                                  pvc.yaml                                           
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi


controlplane ~ ➜  k get pvc
NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
my-pvc   Bound    pv-1     10Mi       RWO  

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: use-pv
  name: use-pv
spec:
  volumes:
    - name: abc
      persistentVolumeClaim:
        claimName: my-pvc
  containers:
  - image: nginx
    name: use-pv
    resources: {}
    volumeMounts:
        - mountPath: "/data"
          name: abc
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

5.

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/


6.
k create deployment nginx-deploy --image=nginx:1.16 --replicas=1
k set image deployment/nginx-deploy nginx=nginx:1.17 --record

controlplane ~ ➜  kubectl rollout history deployment/nginx-deploy
deployment.apps/nginx-deploy 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record=true




7. 

controlplane ~/CKA ➜  k run nginx-resolver --image=nginx
pod/nginx-resolver created

controlplane ~/CKA ➜  k expose pod nginx-resolver --name=nginx-resolver-service --port=80 --type=Cl

controlplane ~/CKA ✖ k run test-lookup --image busybox:1.28 --rm -it --restart=Never -- nslookup ng
inx-resolver-service
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-resolver-service
Address 1: 10.97.213.154 nginx-resolver-service.default.svc.cluster.local
pod "test-lookup" deleted

