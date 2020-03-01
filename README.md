# httpd-tomcat on k8s

# 実装方式

## httpdをリバースプロキシにするパターン

- [このタグ(reverse_proxy)](https://github.com/answer-d/hyper_k8s_obenkyo_time/tree/reverse_proxy)
- やってみたものの…httpdでしかできない特別な機能があるとかじゃなければ、Ingressでいいのでは？って感じ

## http-tomcat間をAJPで連携するパターン

- [このタグ(ajp_connect)](https://github.com/answer-d/hyper_k8s_obenkyo_time/tree/ajp_connect)
- やってみたものの…AJPで連携する必要はないかも…
    - マイクロサービスの思想的にPod間通信は多分RESTにした方が良いだろうし…
    - もしくはプロキシ(サービスメッシュとかそんな感じだよな？)

# ていうか

- そもそもhttpdでL7LBの機能を実現したいだけなら、httpdコンテナじゃなくてIngress使えばいいんじゃね？と思った
- のでやってみる

## Ingressパターン

### Nginx Ingress Controller導入

- Docker for MacなのでNginx Ingress Controllerを入れる、公式サイトに従ってやる
    - <https://kubernetes.github.io/ingress-nginx/deploy/>
- コマンド2発！めっちゃ簡単！！！！！！！！！！！

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/mandatory.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/provider/cloud-generic.yaml
```

- namespace `ingress-nginx` にいろいろとできてる

```console
# kubectl get all -n ingress-nginx
NAME                                            READY   STATUS    RESTARTS   AGE
pod/nginx-ingress-controller-7fcf8df75d-ch9q4   1/1     Running   0          78s


NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx   LoadBalancer   10.103.47.200   localhost     80:32605/TCP,443:30149/TCP   73s


NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-ingress-controller   1/1     1            1           78s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-ingress-controller-7fcf8df75d   1         1         1       78s
```

### 実装

- [このタグ(ingress-simple)](https://github.com/answer-d/hyper_k8s_obenkyo_time/tree/ingress-simple)

## おまけ

- ちなみにNginxでもAJPモジュールあるらしい
    - けど上述の理由でAJPを使う理由があまり見えないし、めんどいのでもういいや！

## おわり

# Refs

## k8s関連

### はじめてのConfigMap

- <https://cloud.google.com/kubernetes-engine/docs/concepts/configmap?hl=ja>
- <https://qiita.com/tkusumi/items/8e31fddda77f93ccfdd8>
- <https://qiita.com/petitviolet/items/ee4b1bdba2670a1d6a12>

### ConfigMapで1ファイルだけマウントしたい

- <https://stackoverflow.com/questions/44325048/kubernetes-configmap-only-one-file>

### Nginx Ingress Controller

- <https://kubernetes.github.io/ingress-nginx/deploy/>

## httpd,tomcat関連

### httpd Reverse Proxy

- <https://qiita.com/okiyuki99/items/83c1fb07644cd232d91e>

### httpd-tomcat連携(AJP)

- <https://teratail.com/questions/79438>
- <http://sakusaku-techs.com/apache-tomcat/apche-connect/>
- 地味にハマったんだけど、 `server.xml` に `address="0.0.0.0"` を書かないとlocalhostからの通信しか許可されなかった
    - コンテナ入って `ss -ln` とかやると分かる
