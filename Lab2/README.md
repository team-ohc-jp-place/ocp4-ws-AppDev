Lab2. Jenkinsベースのビルドパイプライン では以下の内容実施します。
- 既存コンテナイメージをシンプルにデプロイ
- Jenkinsを使用したビルドパイプライン体験

# 既存コンテナイメージを使ってOCPにデプロイ
- 既存コンテナイメージ -> OCP上にデプロイ
- Dockerfile -> コンテナイメージビルド -> OCP上にデプロイ
  - s2ibuildできる環境が必要
  - cri-o + buildah環境(RHEL8)が必要

Lab1ではソースコードとbuidler imageを合体させてコンテナイメージを作成し，OCP上にデプロイしました。Lab2の最初のステップでは，**既にコンテナイメージ化済**のターミナルアプリケーションをOCP上にデプロイする手順を実施します。

1. プロジェクト名を指定します
    
    プロジェクト名には，**必ずご自身のログイン時のユーザー名 (例: "user01a")** を指定してください。    
    Home > Project > user01a (例)
    
    ![](images/create_application_using_existedImage_1.png)

1. **Add > Deploy Image** のように選択します

    ![](images/create_application_using_existedImage_2.png)

1. **Namespace**(プロジェクト名)，と**Image Name** を指定します
    - Namespace: `各自の作成済プロジェクト(例: user01a)`
    - Image Name: `quay.io/openshiftlabs/workshop-terminal:2.4.0`

    ![](images/create_application_using_existedImage_3.png)

1. **検索ボタン** をクリックし，Name(workshop-terminal)を確認して，**Deploy** を選択します

    ![](images/create_application_using_existedImage_4.png)

1. 外部からアクセスするための **Route** を作成します

    Networking > Routes > Create Route を選択し，以下を指定した後 **Create** を選択します
    - Name: `Route名(例: user01a-workshop-terminal)`
    - Service: `対象アプリ用のService(例: user01a-workshop-terminal)`
    - Target Port: `10080 → 10080(TCP)`

    ![](images/create_route_for_existedImage.png)

1. Location欄にあるリンクを開きます
    例: `http://user01a-workshop-terminal-myprj.apps.ocp41-ipi-0611.k8show.net`

    ![](images/create_route_for_existedImage_result.png)

1. Terminalアプリが表示されることを確認します

    ![](images/create_route_for_existedImage_result_2.png)

# [チカラ試し] - OCP上にアプリをデプロイ2
お題: 

「**workshop-terminalアプリの特定バージョン(2.10.2)を新規にデプロイして，Routerの振り先を変更してみよう**」

ヒント:
- 対象アプリ(workshop-terminal)は既にコンテナイメージ化されている
- 対象のバージョンは，2.10.2
- **Routes** は先程作ったもの(例: user01a-workshop-terminal)を編集する

# Jenkinsベースのビルドパイプライン

```
oc get templates -n openshift
oc get templates -n openshift | grep jenkins
oc get pods | grep jenkins
oc new-app jenkins-ephemeral
oc get pods
oc project
oc delete jenkins-1-deploy
oc new-project mycicd
oc project
oc get templates -n openshift
oc get templates -n openshift | grep jenkins
oc new-app jenkins-ephemeral -n mycicd
oc get routes
oc get template/jenkins-ephemeral -o json -n openshift
oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/jenkins/pipeline/nodejs-sample-pipeline.yaml
oc get buildconfigs
oc get buildconfig/nodejs-sample-pipeline -o yaml
oc get route
oc start-build nodejs-sample-pipeline
```

OCPコンソール上でbuild congfigのyamlを開いて何かを編集して，再度実行する