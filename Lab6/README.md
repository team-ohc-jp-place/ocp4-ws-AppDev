# Lab6: JenkinsによるCI／CDパイプラインへの組み込み

- Jenkins CI/CDパイプライン構築
- 応用問題

# Jenkins CI/CDパイプライン構築
最後にLab2で作成したjenkins上にLab5で作成したQuarkusプロジェクトを組み込んでjenkins上でビルド、デプロイできるようにします。前回のLab5では御自身で様々なコマンドを打ち込みBuild Configを作成したり、アプリケーションを手動で作成したりしていました。ここではjenkins pipelineという仕組みを使って一連の手順を自動化します。

1. CodeReady 上でterminalを開き、Lab2で作成したjenkinsプロジェクトを選択してください。 


   ex. oc project devXX-jenkins

   ![](images/cicd_1.png)

   また、カレントディレクトリが「sample-quarkus-0.20.0」であることも確認してください。

2. 下記コマンドを入力し、Build Config を作成します。

    ```
    oc create -f openshift/pipeline.yaml
    ```

3. 作成したBuild Config をstart します。これだけでjenkins上で一連の流れがスタートします。

    ```
    oc start-build quarkus-sample-pipeline
    ```

4. jenkinsの画面を開き、pipelineが開始されていることを確認してください。

    ![](images/cicd_2.png)

5. 左下の #1を選択し、次の画面でConsole Outputを選択してください。pipeline実行中のログが確認できます。

    ![](images/cicd_3.png)

6. 下記のような出力が確認できればpipeline は完了です。前回と同じ手順でquarkusアプリケーションのエンドポイントを確認し、画面が表示されているか確認してください。

    ![](images/cicd_4.png)

    ![](images/cicd_5.png)

7. 下記のようにデフォルトページが表示されれば完了です。

    ![](images/cicd_6.png)

# 応用問題

1. openshift/pipeline.yaml 開き、各stageが定義されていることを確認してください。ここに任意の箇所で下記stageを追加してください。

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
   oc apply -f openshift/pipeline.yaml
   ```

2. importしたgithubプロジェクトの 「src/main/java/org/acme/quickstart/GreetingResource.java」の出力を変更、pushした後jenkins上で再度「Build Now」を実行してください。pipeline完了後、/hello エンドポイントの出力が変わっているか確認してください。