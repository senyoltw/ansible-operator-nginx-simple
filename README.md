![Molecule Test](https://github.com/senyoltw/ansible-operator-nginx-simple/workflows/Molecule%20Test/badge.svg)

# ansible-operator-nginx-simple
operator-sdkによるansible operatorのサンプル実装。できるだけシンプルに。

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
ansible-operator-nginx-simple-859dc55dd8-hq244   1/1     Running   0          59s

# nginxをoperator経由でデプロイ
$ oc apply -f deploy/crds/webserver.example.com_v1alpha1_nginx_cr.yaml 
$ oc get pod
NAME                                             READY   STATUS    RESTARTS   AGE
ansible-operator-nginx-simple-859dc55dd8-hq244   1/1     Running   0          80s
example-nginx-nginx-1.19.2-6686d8f89f-595km      1/1     Running   0          20s
example-nginx-nginx-1.19.2-6686d8f89f-9rd8k      1/1     Running   0          20s
example-nginx-nginx-1.19.2-6686d8f89f-wtwbz      1/1     Running   0          20s

$ oc delete Nginx example-nginx
$ oc get pod
NAME                                             READY   STATUS        RESTARTS   AGE
ansible-operator-nginx-simple-859dc55dd8-hq244   1/1     Running       0          2m19s
example-nginx-nginx-1.19.2-6686d8f89f-595km      0/1     Terminating   0          79s
example-nginx-nginx-1.19.2-6686d8f89f-9rd8k      0/1     Terminating   0          79s
example-nginx-nginx-1.19.2-6686d8f89f-wtwbz      0/1     Terminating   0          79s

# Cleanup
$ oc delete -f deploy/crds/webserver.example.com_v1alpha1_nginx_cr.yaml 
$ oc delete -f deploy/operator.yaml
$ oc delete -f deploy/role_binding.yaml
$ oc delete -f deploy/role.yaml
$ oc delete -f deploy/service_account.yaml
$ oc delete -f deploy/crds/webserver.example.com_nginxes_crd.yaml 
```