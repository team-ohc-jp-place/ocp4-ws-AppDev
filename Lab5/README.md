# Lab5: CodeReady Workspaceを利用した開発体験

- CodeReady Workspaceのインストール
- サンプルアプリケーションの開発とOpenshiftへのデプロイ

# CodeReady Workspaceのインストール
OpenShift上にCodeReadyをインストールします。

1. プロジェクトを選択します

   プロジェクトは，**必ずご自身のログイン時のユーザー名 (例: "dev01")** のものを選択してください。
   Home > Project > dev01　 (例)

   ![](images/create_application_using_existedImage_1.png)

2. Catalog > OperatorHub と進み、検索窓に「CodeReady」と入力してください。CodeReadyのitemが表示されたら選択してください。

    ![](images/install_1.png)

3. installします。

    ![](images/install_2.png)

4. 御自身のprojectがインストール先に選択されていることを確認して、Subscribeを選択してください。

    ![](images/install_3.png)

5. Operatorがinstallされました。これを元にproject にCodeReadyのアプリケーションを作成します。Catalog > Installed Operators と進み、「Red Hat CodeReady Workspaces」を選択します。表示されない場合は、まだOperatorのインストールが終わっていない状態ですのでしばらくたってから再度確認してみてください。

    ![](images/install_4.png)

6. Create Newを選択します。

    ![](images/install_5.png)

7. そのままCreateします。

    ![](images/install_6.png)

8. Createが開始されました。時間がかかるのでしばらくお待ちください。

    ![](images/install_7.png)

9. Home > Status と進み、下記のように必要なResouceが作成されたら完了です。

    ![](images/install_8.png)

10. Networking > Routes と進み、Routeが二つあることを確認します。codereadyのLocationに表示されているURLを選択してください。

    ![](images/install_9.png)

11. ログイン画面が表示されますので、Registerを選択してアカウントを作成します。

     ![](images/install_10.png)

12. 任意で入力してRegisterを選択してください。

    ![](images/install_11.png)

13. 下記のような画面が表示されたらCodeReadyの準備は完了です。

    ![](images/install_12.png)

# サンプルアプリケーションの開発とOpenshiftへのデプロイ

ここからインストールしたCodeReady上で[Quarkus](https://quarkus.io/)アプリケーションを作成、デプロイしていきます。作業していてworkspaceが重いと感じる場合は、このセクションの一番下にあるtipsを元にworkspaceに割り当てるメモリを増やしてみてください。

1. Dashboard を選択し、下記画面を開いたらJava1.8を選択して 「CREATE & OPEN」を選択してください。WorkSpaceの作成が始まります。時間がかかるのでしばらくお待ちください。

   ![](images/app_1.png)
   
2. 下記画面が開いたら Import Projectを選択してください。

   ![](images/app_2.png)

3. 「GITHUB」を選択し、下記URLを入力したらImportしてください。

   ```
   https://github.com/16yuki0702/sample-quarkus-0.20.0.git
   ```

   ![](images/app_3.png)

4. Project Configurationには JAVA > Mavenを選択して Saveします。

   ![](images/app_4.png)

5. project を右クリックし、Open in Terminalを選択してください。

   ![](images/app_5.png)

6. 右下にTerminalが開かれます。試しにコマンド「pwd」と打ってカレントディレクトリが 「/projects/sample-quarkus-0.20.0」であることを確認してください。以降このディレクトリ内で作業を行うので、異なるディレクトリにいた場合は上記ディレクトリに移動してください。Terminalのタブをダブルクリックすると画面が大きくなります。

   ![](images/app_6.png)

7. CodeReady上からOpenshift にログインします。下記コマンドを打ち、それぞれに割り当てられたユーザー名とパスワードを入力してください。

   ```
   oc login https://openshiftクラスタ
   ```

8. ログインしたら下記コマンドで自分専用のproject を作成してください。他とproject名が重複するとエラーになりますので、打ち間違いがないか確認してください。

   ```
   oc new-project ユーザー名-app
   ```

   ex. oc new-project dev11-app

9. プロジェクトを作成したら下記を入力し、正しいprojectにいるか確認してください。

   ```
   oc project
   ```

10. デプロイの準備を行っていきます。下記を入力し、build設定を作成します。

   ```
   APP_NAME=quarkus-app
   oc new-build --strategy=docker --binary=true --name=$APP_NAME
   ```

   下記のようにsuccessと出れば成功です。

   ![](images/app_7.png)

11. 下記コマンドを実行してビルドを開始します。このコマンドでは作成したビルド設定に対し自分の作業ディレクトリを指定しています。このようにすると作業中のディレクトリをopenshfitに転送してビルドすることができます。—followオプションを付けているのでbuild実行中のログが流れます。ビルドには時間がかかりますのでしばらくお待ちください。

    ```
    oc start-build $APP_NAME --from-dir=/projects/sample-quarkus-0.20.0 --follow
    ```

    下記のように「push successful」と出れば成功です。

    ![](images/app_8.png)

12. ビルドしたイメージを元にアプリケーションを作成します。

    ```
    oc new-app $APP_NAME
    ```

    下記のように表示されれば作成成功です。

    ![](images/app_9.png)

13. サービス公開設定をします。

    ```
    oc expose service $APP_NAME
    ```

14. エンドポイントの確認をします。

    ```
    oc get route
    ```

    Locationの箇所にあるURLがサービスエンドポイントになります。URLにアクセスしてみてください。

    ![](images/app_10.png)

15. アクセスして下記のような画面が表示されればデプロイ成功です。

    ![](images/app_11.png)



### 応用問題

1. src/main/java/org/acme/quickstart/GreetingResource.java の中身を確認してみてください。helloと文字列を返しているクラスのはずです。デフォルトページではなく、GreetingResource.javaの画面を表示してみてください

2. src/main/java/org/acme/quickstart/ChallengeResource.java ファイルを下記のように作成し、再度デプロイしてみてください。追加されたChallengeResource.javaの画面が表示できるか確認してみてください。

   ```
   package org.acme.quickstart;
   
   import javax.ws.rs.GET;
   import javax.ws.rs.Path;
   import javax.ws.rs.PathParam;
   import javax.ws.rs.Produces;
   import javax.ws.rs.core.MediaType;
   
   @Path("/challenge")
   public class ChallengeResource {
       @GET
       @Produces(MediaType.TEXT_PLAIN)
       @Path("{param}")
       public String challenge(@PathParam("param") String param) {
           return String.format("Hello %s!", param);
       }
   }
   ```

### tips

CodeReadyを操作していて重いと感じる場合は、割り当てるメモリが少ない可能性があります。下記手順でWorkspaceに割り当てるメモリを増設できます。

1. Workspaceを開き、対象のWorkspaceが停止になっていることを確認してください。その後、行右側の歯車のマークを選択してください。

   ![](images/tips_1.png)

2. Environments > default > machines > dev-machine > attributes > memoryLimitBytes を見ると 「2147483648」となっています。「4294967296」とすると2GBから4GBに変更になります。変更すると自動的にsaveが始まります。右上にsucceccfulと出たら完了です。そのまま Runすると4GBでWorkspaceが開始されます。

   ![](images/tips_2.png)


