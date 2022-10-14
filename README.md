# Oracle Hangout Cafe Season 6 #3 Workflows

セッション時に使用したデモ環境の構築手順および資材置き場です。

# Argo Workflows Set Up

## 1.Argo Workflows Install

デモ環境は、Helm でインストールを実施したので、その手順となります。

事前に Helm クライアントをインストールが必要となります。

Oracle Cloud Infrastructure(OCI) の場合は、Cloud Shell を利用することで Helm クライアントのインストールは不要です。

```sh
helm repo add argo https://argoproj.github.io/argo-helm
```

```sh
helm install --create-namespace --namespace argo argo-workflows argo/argo-workflows
```
```sh
NAME: argo-workflows
LAST DEPLOYED: Fri Oct 14 05:45:33 2022
NAMESPACE: argo
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get Argo Server external IP/domain by running:

kubectl --namespace argo get services -o wide | grep argo-workflows-server

2. Submit the hello-world workflow by running:

argo submit https://raw.githubusercontent.com/argoproj/argo-workflows/master/examples/hello-world.yaml --watch
```

[Argo Workflows Chart] (https://github.com/argoproj/argo-helm/tree/main/charts/argo-workflows)

argo-workflows-server と argo-workflows-workflow-controller の Pod が Running であることを確認します。

```sh
kubectl get pods -n argo
```
```sh
NAME                                                  READY   STATUS    RESTARTS   AGE
argo-workflows-server-6498c4c886-f8g24                1/1     Running   0          4m4s
argo-workflows-workflow-controller-8674b6b66d-26ks4   1/1     Running   0          4m4s
```

argo workflows のインストールは完了です。

## 2.Argo Workflows Access

デモ環境では、Service としてインストールされる argo-workflow-service の type を LoadBalancer に変更して、
ブラウザからアクセスできるようにします。

```sh
kubectl edit service/argo-workflows-server -n argo
```
```sh
・
・ ＜省略＞
・
  selector:
    app.kubernetes.io/instance: argo-workflows
    app.kubernetes.io/name: argo-workflows-server
  sessionAffinity: None
  type: ClusterIP # type: LoadBalancer に変更
・
・ ＜省略＞
・
```

EXTERNAL-IP を確認

```sh
kubectl get services -n argo
```
```sh
NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)          AGE
argo-workflows-server   LoadBalancer   10.96.147.139   193.xxx.xxx.xxx   2746:31787/TCP   24m
```

ブラウザを起動して、UI にアクセスします。

http://193.xxx.xxx.xxx:2746/

![Login 1](images/01.png)

Token を取得して、ログインします。

argo-workflows-server Podのコンテナから argo auth token コマンドを実行して Token を取得します。

argo-workflows-server の Pod 名を確認します。

```sh
kubectl get pods -n argo
```
```sh
NAME                                                  READY   STATUS    RESTARTS   AGE
argo-workflows-server-6498c4c886-f8g24                1/1     Running   0          36m
argo-workflows-workflow-controller-8674b6b66d-26ks4   1/1     Running   0          36m
```

```sh
kubectl exec -it argo-workflows-server-6498c4c886-f8g24 -n argo -- argo auth token
```
```sh
Bearer eyJ...
```

Bearer を含めて、ログイン画面内で入力して、ログインボタンをクリックします。

![Login 2](./images/02.png)

ログイン完了です。

![Login 3](./images/03.png)

