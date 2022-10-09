### 使用helm搭建dashboard按照教程使用helm搭建dashboard的pod[参考网站](https://artifacthub.io/packages/helm/k8s-dashboard/kubernetes-dashboard)

1. 配置sa.yml, kubectl apply -f sa.yaml

   ```
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     name: admin-user
     namespace: default
   ```
2. 配置rbac.yaml, kubectl apply -f rbac.yaml

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: admin-user
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: cluster-admin
   subjects:
   - kind: ServiceAccount
     name: admin-user
     namespace: default
   ```
3. 得到admin-user token

   ```
   kubectl -n default  describe secret $(kubectl -n default  get secret | grep admin-user | awk '{print $1}')
   ```
4. (1) https在helm install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard 后会有指令提示,在终端根据指令提示输入以下命令

   ```

   export POD_NAME=$(kubectl get pods -n default -l "app.kubernetes.io/name=kubernetes-dashboard,app.kubernetes.io/instance=kubernetes-dashboard" -o jsonpath="{.items[0].metadata.name}") 

   echo https://127.0.0.1:8443/

   kubectl -n default port-forward $POD_NAME 8443:8443

   (kubectl -n default port-forward $POD_NAME 8443:8443 --address 0.0.0.0)
   ```
5. (2) http模式

   终端输入

   ```

    kubectl proxy

   ```
   后台模式后面加个&

   ```

   如需内网其他机器访问,可以在其他机器上使用ssh代理的方式

   ```
   ssh -L localhost:8001:localhost:8001 -NT root@192.168.1.54

   ```

   http://localhost:8001/api/v1/namespaces/default/services/https:kubernetes-dashboard:https/proxy/

   ```
   ```

   ```
