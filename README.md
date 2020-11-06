![Molecule Test](https://github.com/senyoltw/ansible-operator-nginx-simple/workflows/Molecule%20Test/badge.svg)

# ansible-operator-nginx-simple
operator-sdkによるansible operatorのサンプル実装。できるだけシンプルに。

# operator動作内容
- nginx podのServiceを設定
- nginxinc/nginx-unprivileged をデプロイ
  - size の数だけpodを起動
  - image_version でコンテナのバージョン指定

# 作成・動作・テスト環境  
- 作成・動作
  - OpenShift/oc 4.4.17
  - operator-sdk 0.19.4
- テスト
  - GitHub Actions with Molecule
    - Kubernetes 1.16
    - Kubernetes 1.17

# よさげなドキュメント
https://v0-19-x.sdk.operatorframework.io/docs/  
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.4/html/operators/index

# 使用方法
```
# operatorデプロイ
$ git clone https://github.com/senyoltw/ansible-operator-nginx-simple
$ cd ansible-operator-nginx-simple

$ oc new-project ansible-operator-nginx-simple-prj
$ oc create -f deploy/crds/webserver.example.com_nginxes_crd.yaml 
$ oc create -f deploy/service_account.yaml
$ oc create -f deploy/role.yaml
$ oc create -f deploy/role_binding.yaml
$ oc apply -f deploy/operator.yaml

$ oc get pod
NAME                                             READY   STATUS    RESTARTS   AGE
ansible-operator-nginx-simple-6fd87b5db9-q9zwk   1/1     Running   0          14s

# nginxをoperator経由でデプロイ
$ oc apply -f deploy/crds/webserver.example.com_v1alpha1_nginx_cr.yaml 
$ oc get all
NAME                                                 READY   STATUS    RESTARTS   AGE
pod/ansible-operator-nginx-simple-6fd87b5db9-q9zwk   1/1     Running   0          37s
pod/example-nginx-nginx-1.19.2-6686d8f89f-2qr2c      1/1     Running   0          7s
pod/example-nginx-nginx-1.19.2-6686d8f89f-smpqv      1/1     Running   0          7s
pod/example-nginx-nginx-1.19.2-6686d8f89f-snljn      1/1     Running   0          7s

NAME                                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/ansible-operator-nginx-simple-metrics   ClusterIP   172.30.186.184   <none>        8383/TCP,8686/TCP   16s
service/example-nginx-nginx                     ClusterIP   172.30.38.101    <none>        8080/TCP            8s

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ansible-operator-nginx-simple   1/1     1            1           37s
deployment.apps/example-nginx-nginx-1.19.2      3/3     3            3           7s

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/ansible-operator-nginx-simple-6fd87b5db9   1         1         1       37s
replicaset.apps/example-nginx-nginx-1.19.2-6686d8f89f      3         3         3       7s

$ oc delete Nginx example-nginx
$ oc get all
NAME                                                 READY   STATUS    RESTARTS   AGE
pod/ansible-operator-nginx-simple-6fd87b5db9-q9zwk   1/1     Running   0          76s

NAME                                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/ansible-operator-nginx-simple-metrics   ClusterIP   172.30.186.184   <none>        8383/TCP,8686/TCP   55s

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ansible-operator-nginx-simple   1/1     1            1           76s

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/ansible-operator-nginx-simple-6fd87b5db9   1         1         1       76s

# Cleanup
$ oc delete -f deploy/crds/webserver.example.com_v1alpha1_nginx_cr.yaml 
$ oc delete -f deploy/operator.yaml
$ oc delete -f deploy/role_binding.yaml
$ oc delete -f deploy/role.yaml
$ oc delete -f deploy/service_account.yaml
$ oc delete -f deploy/crds/webserver.example.com_nginxes_crd.yaml 
```