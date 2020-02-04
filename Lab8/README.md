# Lab8: Service Meshを使ったアプリケーション開発

- Service Mesh上にアプリケーションを構築
- Service Meshを使ったfault injection

# Service Mesh上にアプリケーションを構築

Service Mesh上にアプリケーションを構築していきます。  Service MeshにはOpenShift Service Meshを使用します。 
https://docs.openshift.com/container-platform/4.2/service_mesh/service_mesh_arch/understanding-ossm.html

CodeReadyを使用している場合はCodeReadyのTerminal、踏み台サーバーを使用している場合は踏み台上で作業を行なってください。また、OpenShfitにログインしているかどうか確認してください。ログインしていない場合は下記でログインができます。

```
oc login https://api.xxx:6443
```

1. homeディレクトリに移動し、プロジェクトをcloneしてください。

  ```
  cd
  git clone https://github.com/16yuki0702/tracing-app.git
  ```

2. アプリケーションで必要な設定をするために、下記でセットアップを行います。ユーザー名の箇所には御自身のユーザー名を入力してください。 ex. user01

   ```
   cd tracing-app
   ./setup.sh ユーザー名
   ```
   
3. 続けて下記でアプリケーションのビルド, デプロイを行います。

   ```
   ./build.sh
   ./deploy.sh
   ```

4. 下記を実行してアプリケーションにリクエストを送ります。

   ```
   ./test.sh
   ```

5. kialiのダッシュボードを確認してアプリケーションの状況を確認します。下記でURLを確認し、頭にhttpsをつけてブラウザから遷移してください。

   ```
   oc get route kiali --template='{{ .spec.host }}' -n ユーザー名-smcp
   ```

6. SSOを求められたら、OCPログイン時と同じ ユーザー名/パスワード を入力してください。ダッシュボードに遷移したら、下記画像の様に「Namespace」に ユーザー名-tracingを選択し、「DIsplay」ボタンを押してTraffic Animationにチェックを入れてください。リクエストの状況がアニメーションで確認できます。

   ![](images/ossm_1.png)

# Service Meshを使ったfault injection

1. 下記yamlファイルを作り(ユーザー名の箇所はご自身のものに変更してください)、oc apply -f yaml で変更を適用してください。

   ```
   oc apply -f - <<EOF
   apiVersion: networking.istio.io/v1alpha3
   kind: VirtualService
   metadata:
     name: ユーザー名-gateway
     namespace: ユーザー名-tracing
   spec:
     hosts:
     - "*"
     gateways:
     - ユーザー名-gateway
     http:
     - match:
       - uri:
           exact: /
       - uri:
           exact: /propagate1
       - uri:
           exact: /propagate2
       - uri:
           exact: /propagate3
       route:
       - destination:
           host: ユーザー名-gateway
           port:
             number: 8080
       fault:
         abort:
           httpStatus: 503
           percent: 50
   EOF
   ```

2. ダッシュボードを確認し、リクエストが失敗する時としない時があることを確認してください。

   ![](images/ossm_2.png)

# 応用問題

Tracingをより詳細に確認できるJaegerというアプリケーションも今回のハンズオンで用意されています。URLを調べてJaegerのダッシュボードを確認し、Tracingがどのように表示されるか確認してみてください。

![](images/jaeger_1.png)