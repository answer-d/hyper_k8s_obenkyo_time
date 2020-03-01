# httpd-tomcat on k8s

# 実装方式

## httpdをリバースプロキシにするパターン

- [このブランチ](https://github.com/answer-d/hyper_k8s_obenkyo_time/tree/reverse_proxy)
- やってみたものの…httpdでしかできない特別な機能があるとかじゃなければ、Ingressでいいのでは？って感じ

## http-tomcat間をAJPで連携するパターン

- [このブランチ](https://github.com/answer-d/hyper_k8s_obenkyo_time/tree/ajp_connect)
- やってみたものの…AJPで連携する必要はないかも…
    - マイクロサービスの思想的にPod間通信は多分RESTにした方が良いだろうし…
    - もしくはプロキシ(サービスメッシュとかそんな感じだよな？)

# ていうか

- そもそもhttpdでL7LBの機能を実現したいだけなら、httpdコンテナじゃなくてIngress使えばいいんじゃね？と思った
- のでやってみる

## nginx Ingressパターン

- Todo
- nginxでもAJPモジュールあるらしい？

# Refs

## k8s関連

### はじめてのConfigMap

- <https://cloud.google.com/kubernetes-engine/docs/concepts/configmap?hl=ja>
- <https://qiita.com/tkusumi/items/8e31fddda77f93ccfdd8>
- <https://qiita.com/petitviolet/items/ee4b1bdba2670a1d6a12>

### ConfigMapで1ファイルだけマウントしたい

- <https://stackoverflow.com/questions/44325048/kubernetes-configmap-only-one-file>

## httpd,tomcat関連

### httpd Reverse Proxy

- <https://qiita.com/okiyuki99/items/83c1fb07644cd232d91e>

### httpd-tomcat連携(AJP)

- <https://teratail.com/questions/79438>
- <http://sakusaku-techs.com/apache-tomcat/apche-connect/>
- 地味にハマったんだけど、 `server.xml` に `address="0.0.0.0"` を書かないとlocalhostからの通信しか許可されなかった
    - コンテナ入って `ss -ln` とかやると分かる
