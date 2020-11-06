![Molecule Test](https://github.com/senyoltw/ansible-operator-nginx-simple/workflows/Molecule%20Test/badge.svg)

# ansible-operator-nginx-simple
operator-sdkによるansible operatorのサンプル実装。できるだけシンプルに。

# 作成・動作環境  
- OpenShift/oc 4.4.17
- operator-sdk 0.19.4

# よさげなドキュメント
https://v0-19-x.sdk.operatorframework.io/docs/  
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.4/html/operators/index

# 使用方法
```
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
ansible-operator-nginx-simple-7b7575cb5f-7vd77   1/1     Running   0          74s

$ oc apply -f deploy/crds/webserver.example.com_v1alpha1_nginx_cr.yaml 
$ oc get pod
NAME                                             READY   STATUS    RESTARTS   AGE
ansible-operator-nginx-simple-7b7575cb5f-7vd77   1/1     Running   0          76s
example-nginx-nginx-59dcc47779-cnzh2             1/1     Running   0          8s
example-nginx-nginx-59dcc47779-mtdrv             1/1     Running   0          8s
example-nginx-nginx-59dcc47779-pdpn6             1/1     Running   0          8s
$ oc delete Nginx example-nginx
$ oc get pod
NAME                                             READY   STATUS        RESTARTS   AGE
ansible-operator-nginx-simple-7b7575cb5f-7vd77   1/1     Running       0          84s
example-nginx-nginx-59dcc47779-cnzh2             0/1     Terminating   0          16s
example-nginx-nginx-59dcc47779-mtdrv             0/1     Terminating   0          16s
example-nginx-nginx-59dcc47779-pdpn6             0/1     Terminating   0          16s

# Cleanup
$ oc delete -f deploy/crds/webserver.example.com_v1alpha1_nginx_cr.yaml 
$ oc delete -f deploy/operator.yaml
$ oc delete -f deploy/role_binding.yaml
$ oc delete -f deploy/role.yaml
$ oc delete -f deploy/service_account.yaml
$ oc delete -f deploy/crds/webserver.example.com_nginxes_crd.yaml 
```