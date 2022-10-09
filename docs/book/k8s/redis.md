https://www.gotkx.com/?p=76

helm 安装redis


helm repo add my-repo https://charts.bitnami.com/bitnami

helm install my-release -f values.yaml my-repo/redis

kubectl get pod -A -o wide

kubectl describe  -n databases pod my-release-redis-replicas-0

kubectl logs 


helm delete my-release

```
[root@master redis]# helm repo add my-repo https://charts.bitnami.com/bitnami
"my-repo" has been added to your repositories
[root@master redis]# helm install my-release -f values.yaml my-repo/redis
NAME: my-release
LAST DEPLOYED: Sun Oct  9 11:03:37 2022
NAMESPACE: databases
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: redis
CHART VERSION: 17.3.2
APP VERSION: 7.0.5

** Please be patient while the chart is being deployed **

Redis® can be accessed on the following DNS names from within your cluster:

    my-release-redis-master.databases.svc.cluster.local for read/write operations (port 6379)
    my-release-redis-replicas.databases.svc.cluster.local for read-only operations (port 6379)



To get your password run:

    export REDIS_PASSWORD=$(kubectl get secret --namespace databases my-release-redis -o jsonpath="{.data.redis-password}" | base64 -d)

To connect to your Redis® server:

1. Run a Redis® pod that you can use as a client:

   kubectl run --namespace databases redis-client --restart='Never'  --env REDIS_PASSWORD=$REDIS_PASSWORD  --image docker.io/bitnami/redis:7.0.5-debian-11-r3 --command -- sleep infinity

   Use the following command to attach to the pod:

   kubectl exec --tty -i redis-client \
   --namespace databases -- bash

2. Connect using the Redis® CLI:
   REDISCLI_AUTH="$REDIS_PASSWORD" redis-cli -h my-release-redis-master
   REDISCLI_AUTH="$REDIS_PASSWORD" redis-cli -h my-release-redis-replicas

To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace databases svc/my-release-redis-master 6379:6379 &
    REDISCLI_AUTH="$REDIS_PASSWORD" redis-cli -h 127.0.0.1 -p 6379
```
