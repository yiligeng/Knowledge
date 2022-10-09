```
kubectl run mongodb-client --rm --tty -i --restart='Never' --image docker.io/bitnami/mongodb:4.4.10-debian-10-r20 --command -- bash
```

[MongoDB数据库教程](https://www.hangge.com/blog/cache/detail_3159.html)

连接mongo

mongo --host mongodb-0.mongodb

mongo --host mongodb-0.mongodb -u mongouser -p mongopass


mkdir -p /usr/local/k8s/redis/pv1
mkdir -p /usr/local/k8s/redis/pv2
mkdir -p /usr/local/k8s/redis/pv3


/usr/local/k8s/redis/pv1 *(rw,sync,no_root_squash)
/usr/local/k8s/redis/pv2 *(rw,sync,no_root_squash)
/usr/local/k8s/redis/pv3 *(rw,sync,no_root_squash)