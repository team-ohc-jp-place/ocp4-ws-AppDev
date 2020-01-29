# Lab6: Jenkinsベースのビルドパイプラインへの組込

- Jenkins CI/CDパイプライン構築
- 応用問題

# Jenkins CI/CDパイプライン構築
Lab5で作成したQuarkusプロジェクトをjenkinsベースのビルドパイプラインへ組み込み、jenkins上でビルド、デプロイできるようにします。前回のLab5では御自身で様々なコマンドを打ち込みBuild Configを作成したり、アプリケーションを手動で作成したりしていました。ここではjenkins pipelineという仕組みを使って一連の手順を自動化します。

1. CodeReady 上で Terminalを開き、Lab5で作成したプロジェクトを選択してください。

   (ex. oc project user01-app)

   また、カレントディレクトリが「/projects/sample-quarkus-0.20.0」であることも確認してください。

2. jenkinsを自身のprojectにインストールします。

    ```
    oc new-app jenkins-ephemeral
    ```

3. Jenkinsのメモリ上限を増やします。openshiftコンソールから Advanced > Projects > user01-app > Workloadsタブ > jenkinsの行 を選び、右上の「Actions」から「Edit Deployment Config」を選択してください。

    ![](images/jenkins_edit_deploymentconfig_1.png)

4. spec.template.spec.containers.resources.limiits.memoryを2Giに変更してsaveしてください。jenkins podのUpdateが始まるので、podが入れ替わるまでしばらくお待ちください。

    ![](images/jenkins_edit_deploymentconfig_2.png)

5. Lab5で作成したBuild Config等を削除して作り直していきます。下記でApplicatoinの設定を全て削除します。(Hands-on中にproject内にゴミが溜まってうまく動かなくなった場合も、下記でApplicatoin設定を削除してみてください)

    ```
    ./jenkins/delete-quarkus-app.sh
    ```

6. 下記コマンドを入力し、Build Config を作成します。

    ```
    oc create -f jenkins/pipeline.yaml
    ```

7. 作成したBuild Config をstart します。これだけでjenkins上で一連の流れがスタートします。

    ```
    oc start-build quarkus-sample-pipeline
    ```

8. jenkinsの画面を開き、pipelineが開始されていることを確認します。

    ```
    $ oc get route
    出力結果のLocation情報をコピーしてブラウザで確認します
    ```

    OCPのログイン情報を使用してJenkinsのUIにログインします

    ![](images/jenkins_login_1.png)

    users.htpasswdを選択し，その後ログイン情報を入力します(例: user01/openshift)

    ![](images/jenkins_login_2.png)

    **自身のプロジェクト名** を選択します(例: user01-app)

    ![](images/jenkins_ui_1.png)

    **プロジェクト名/パイプライン名** を選択します (例: user01-app/quarkus-sample-pipeline)

    ![](images/jenkins_ui_2.png)

    時間経過とともにパイプラインのステージがだんだん右側に伸びていくことが確認できます

    ![](images/cicd_1.png)

9. 左下の #1を選択し、次の画面でConsole Outputを選択してください。pipeline実行中のログが確認できます。

    ![](images/cicd_2.png)

10. 下記のような出力が確認できればpipeline は完了です。前回と同じ手順でquarkusアプリケーションのエンドポイントを確認し、画面が表示されているか確認してください。

    ![](images/cicd_3.png)

    ![](images/cicd_4.png)

11. 下記のようにデフォルトページが表示されれば完了です。

    ![](images/cicd_5.png)

# 応用問題

1. jenkins/pipeline.yaml 開き、各stageが定義されていることを確認してください。ここに任意の箇所で下記stageを追加してください。

   ```
   stage('Test Stage') {
       steps {
           script {
               openshift.withCluster() {
                   openshift.withProject() {
                       echo "Test Stage !!!!"
                   }
               }
           }
       }
   }
   ```

   編集したら下記で変更したpipelineを適用します。その後再度jenkins上で「Build Now」を選択してpipelineを開始し、出力がどのように変化するか確認してください。

   ```
   oc apply -f jenkins/pipeline.yaml
   ```

2. importしたgithubプロジェクトの 「src/main/java/org/acme/quickstart/GreetingResource.java」の出力を変更、pushした後jenkins上で再度「Build Now」を実行してください。pipeline完了後、/hello エンドポイントの出力が変わっているか確認してください。  
   下記2パターンでリポジトリの内容を変更できます。好きな方をお選びください。    
   1. リポジトリをforkし、forkしたリポジトリの内容を変更、push。(jenkins/pipeline.yamlのリポジトリ指定の変更も忘れずに)  
   2. 講師がfork元リポジトリの内容を変更してpush

   ![](images/cicd_4.png)