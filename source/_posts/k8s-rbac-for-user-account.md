---
title: Create an invidual account, and assign the right permissions in k8s
date: 2022-09-28 10:46:37
tags:
    - k8s
---
in a project, we have to manage a Kubernetes on premise - not in cloud.
we need to give people in my team access to kubernetes cluster, without adminitrator role.
for example: the developer member can deploy app into `dev` namespace only.
How do resolve it?
`UserAccount`, `ClusterRole`, `Role`, these objects are solutions. 
in this post, i take a note `how to make sure a user has the right permissions towards the right resources using the Role-Based Access Control model.`
<i>its definition will be skipped </i>
  
there are two concepts but Kubernetes does not have objects which represent normal user accounts. Normal users cannot be added to a cluster through an API call.
```
User account is for humans. 
Service account is for processes, which run in pods.
```

---
### Creating individual certificates for cluster users
- Create private key

(i have no knowledge about these keys, they are ambiguous).
```
the most easiest to understand article.
https://qiita.com/miyuki_samitani/items/e038e2ca7d8b6443657d

申込者が秘密鍵の作成
秘密鍵 + ディスティングイッシュネーム(申込者の情報) = CSRファイルを生成
認証局の申請の際にCSRをつけて申し込み
認証局はCSRのディスティングイッシュネームと申込者の情報が一致するか確認する
認証局は証明書に署名を行い、証明書を発行する
認証局が申込者に証明書、秘密鍵、中間証明書を送る
```
```sh
openssl genrsa -out monkey192.pem 2048

ll
total 8
-rw-r--r--  1 monkey192  staff  1679 Sep 28 11:18 monkey192.pem
```
- Create CSR file (Certificate Sign Requesting)
```sh
openssl req -new -key monkey192.pem -out monkey192.csr -subj "/CN=dev"

-rw-r--r--  1 monkey192  staff   883 Sep 28 11:21 monkey192.csr
-rw-r--r--  1 monkey192  staff  1679 Sep 28 11:18 monkey192.pem
```
- convert csr file to base64 text
```sh
$ cat knqyf263.csr | base64 | tr -d '\n'
```

- Request signing process to k8s cluster
```yaml
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: monkey192-request
spec:
  request: <the above csr's base64 text>
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
EOF
```
```
certificatesigningrequest.certificates.k8s.io/monkey192-request created
```
- Approve certificate signing reques
```
kubectl get csr
NAME                AGE   SIGNERNAME                            REQUESTOR          REQUESTEDDURATION   CONDITION
monkey192-request   54s   kubernetes.io/kube-apiserver-client   kubernetes-admin   24h                 Pending
```
```
kubectl certificate approve monkey192-request
certificatesigningrequest.certificates.k8s.io/monkey192-request approved
```

- Get the certificate from the CSR:
The certificate value is in Base64-encoded format under status.certificate.
Export the issued certificate from the CertificateSigningRequest.
```
$ kubectl get csr monkey192-request -o jsonpath='{.status.certificate}' | base64 -d > monkey192.crt
```

- setting your local kubectl context
```sh
kubectl config set-cluster kubernetes --insecure-skip-tls-verify=true --server=<your cluster endpoint>
kubectl config set-credentials monkey192 --client-certificate=monkey192.crt --client-key=monkey192.pem --embed-certs=true
kubectl config set-context developer --cluster=kubernetes --user=monkey192
Cluster "kubernetes" set.
User "monkey192" set.
Context "developer" created.
```
- swicth to created context
```sh
kubectl config use-context developer
Switched to context "developer".
```

- try get pods
```
kubectl get pods
Error from server (Forbidden): pods is forbidden: User "dev" cannot list resource "pods" in API group "" in the namespace "******"
```
- create ClusterRole & ClusterRoleBinding
for demonstration, i just create a role which allow view pods only
```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: clusterrole-view-only
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

```

- assign ClusterRole to `dev` user
```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: clusterrolebinding-monkey192
subjects:
- kind: User
  name: dev
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: clusterrole-view-only
  apiGroup: rbac.authorization.k8s.io
```
- try to get pods list 
```sh
kubectl get pods
NAME                                  READY   STATUS    RESTARTS   AGE
elasticsearch-565dfb8c5f-gj4d9        1/1     Running   1          18h
```
everthing is going on!
- try to get list services
```
kubectl get services
Error from server (Forbidden): services is forbidden: User "dev" cannot list resource "services" in API group "" in the namespace "******"
```
the `dev` user can not get list of services, because the associated role is not allowed

when using k8s, there are many things need to discover for improvement security.
All in all, create users for Kubernetes clusters without giving everyone the admin certificate is the best security practice.
and <i>this is a note for me!</i>
