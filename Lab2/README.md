# Lab2: Jenkinsベースのビルドパイプライン
- 既存コンテナイメージをシンプルにデプロイ
- Jenkinsを使用したビルドパイプライン体験

# 既存コンテナイメージを使ってOCPにデプロイ

前半ではs2iビルド(ソースコードをビルドテンプレートとbuidler imageによってビルドする方法)によってコンテナイメージを作成し，OCP上にデプロイしました。後半の最初のステップでは，**すでにコンテナイメージになっている**ターミナルアプリケーションをOCP上にデプロイします。

1. プロジェクトを選択します

    プロジェクトは，**必ずご自身のログイン時のユーザー名 (例: "user01")** のものを選択してください。    
    Home > Project > user01 (例)
    
    ![](images/create_application_using_existedImage_1.png)

2. **Add > Deploy Image** を選択します

    ![](images/create_application_using_existedImage_2.png)

3. **Namespace**(プロジェクト名)，と**Image Name** を指定します
    - Namespace: `各自の作成済プロジェクト(例: user01)`
    - Image Name: `quay.io/openshiftlabs/workshop-terminal:2.4.0`

    ![](images/create_application_using_existedImage_3.png)

4. **検索ボタン**(虫眼鏡アイコン)をクリックし，Name(workshop-terminal)を確認して，**Deploy** を選択します

    ![](images/create_application_using_existedImage_4.png)

5. 外部からアクセスするための **Route** を作成します

    Networking > Routes > Create Route を選択し，以下を指定した後 **Create** を選択します
    - Name: `Route名(例: workshop-terminal)`
    - Service: `対象アプリ用のService(例: workshop-terminal)`
    - Target Port: `10080 → 10080(TCP)`

    ![](images/create_route_for_existedImage.png)

6. Location欄にあるリンクを開きます
    例: `http://workshop-terminal-user01.apps.cluster-tokyo-ef76.tokyo-ef76.openshiftworkshop.com/`

    ![](images/create_route_for_existedImage_result.png)

7. Terminalアプリが表示されることを確認します

    ![](images/create_route_for_existedImage_result_2.png)

# [Trial works] - OCP上にアプリをデプロイ2
お題: 

「**workshop-terminalアプリの特定バージョン(2.10.2)を新規にデプロイして，Routerの転送先サービスを変更してみよう**」

ヒント:

```
- 既存Project名(Namespace): <yourID>
- ContainerImage: quay.io/openshiftlabs/workshop-terminal:2.10.2
- 新規デプロイ時に指定する名前: workshop-terminal-v2
- Service名: workshop-terminal-v2
  - Routeからの振り先Service
- Route名: workshop-terminal
  - 振り先を指定する
```

# Jenkinsベースのビルドパイプライン
CI(継続的インテグレーション)ツールとして有名なJenkinsを使ってビルドパイプラインを作成してみましょう。

実際の手順は以下のとおりです。
- JenkinsコンテナをOCP上で動作させる
- Jenkinsにパイプライン設定を入れる

GUIで操作することも可能ですが、今回はCLI操作をメインにして進めてみましょう。

1. 自身用の新規プロジェクト **user01-jenkins** を作成します  **(例: user01-jenkins)**

    ```
    $ oc login https://api.dev.ocp41.nosue.mobi:6443
    $ oc new-project user01-jenkins (<== ご自身のプロジェクト名)
    $ oc project
    Using project "user01-jenkins" on server XXXXXXX
    
    上記のように出力確認できればOKです
    ```

2. Jenkinsテンプレートを使用してJenkinsのインスタンスをデプロイします

    ```
    $ oc get templates -n openshift | grep jenkins

    jenkins-ephemeral: 永続化なし <== 今回はこちらを使用
    jenkins-persistent: 永続化あり

    $ oc new-app jenkins-ephemeral
    $ oc get pods -w
    # ctrl-c でwatch状態から抜けられます
    ```

3. Jenkinsのメモリ上限を増やします。openshiftコンソールから

    Workloads > Deployment Configs > jenkins > YAML と選び、下記画像のようにspec.template.spec.containers.resources.limiits.memoryを2Giに変更してsaveしてください。
    
    ![](images/jenkins_edit_deploymentconfig_1.png)
    
4. Jenkinsにパイプライン設定(nodejs-sample-pipeline)を入れます

    ```
    $ oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/jenkins/pipeline/nodejs-sample-pipeline.yaml
    
    $ oc get buildconfigs
    nodejs-sample-pipeline  # oc createで作成されたPipeline
    
    $ oc get buildconfig/nodejs-sample-pipeline -o yaml　# 中身を確認
    ```

5. パイプラインを使用してビルドします

    ```
    $ oc start-build nodejs-sample-pipeline
    ```

6. JenkinsのUIに接続してパイプラインの進捗状況を確認します

    ```
    $ oc get route
    出力結果のLocation情報をコピーしてブラウザで確認します
    ```
    
    OCPのログイン情報を使用してJenkinsのUIにログインします
    
    ![](images/jenkins_login_1.png)
    
    users.htpasswdを選択し，その後ログイン情報を入力します(例: user01/openshift)
    
    ![](images/jenkins_login_2.png)
    
    **自身のプロジェクト名** を選択します(例: user01-jenkins)
    
    ![](images/jenkins_ui_1.png)

    **プロジェクト名/パイプライン名** を選択します (例: user01-jenkins/nodejs-sample-pipeline)
    
    ![](images/jenkins_ui_2.png)

    時間経過とともにパイプラインのステージがだんだん右側に伸びていくことが確認できます

    ![](images/jenkins_pipeline.png)

    ※パイプラインのステージの書き方は，前述の `oc get buildconfig/nodejs-sample-pipeline -o yaml`で確認できます

>Tips:
>
>ちなみに，Lab1ではOCPコンソール上でGUI操作で上記と同様の作業を行っていました。
>具体的には，**カタログ(Developer Catalog)** からPythonテンプレートを選択して，ソースコードとbuilder image(Python)を合体させることでコンテナイメージを作成し，デプロイしていました。
>また，OCPではJenkinsに限らず，ランタイムや他ミドルウェア，ソフトウェアなど多数のテンプレートを用意しています。自身(自社)でよく使うテンプレートを自作してカタログ上に追加することも可能です。(既存のbuilder imageの挙動をカスタマイズする場合には、S2Iビルドスクリプトを作成してオーバーライドします。)
>
>他にもCI/CDを試したい場合は下記リンクを参照下さい。
>https://adoc.redhat.partners/?https%3A%2F%2Fadoc.redhat.partners%2Flab%2Focp-workshop-dev-cicd.adoc.REPL&no-header-footer&numbered=&toc!
