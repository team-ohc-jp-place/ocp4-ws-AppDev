# Lab5: CodeReady Workspaceを利用した開発体験

- サンプルアプリケーションの開発とOpenshiftへのデプロイ

# サンプルアプリケーションの開発とOpenshiftへのデプロイ

CodeReady上で[Quarkus](https://quarkus.io/)アプリケーションを作成、デプロイしていきます。作業していてworkspaceが重いと感じる場合は、このセクションの一番下にあるtipsを元にworkspaceに割り当てるメモリを増やしてみてください。

1. (既にWorkSpace作成済みの場合はこちらは飛ばしてください) Dashboard を選択し、下記画面を開いたらJava Mavenを選択して 「CREATE & OPEN」を選択してください。WorkSpaceの作成が始まります。時間がかかるのでしばらくお待ちください。

   ![](images/app_1.png)
   
2. 下記画面が開いたら 画面上部Terminalをクリックし、「Open Terminal in specific container」を選択します。選択できるのが「maven」のみとなっているので、mavenを選択してください。

   ![](images/app_2.png)

3. Terminalが開きます。カレントディレクトリは /project となっています。下記コマンドを実行してサンプルアプリケーションをcloneしてください。

   ```
   git clone https://github.com/16yuki0702/sample-quarkus-0.20.0.git
   ```

   ![](images/app_3.png)

4. CodeReady上からOpenshift にログインします。下記コマンドを打ち、それぞれに割り当てられたユーザー名とパスワードを入力してください。

   ```
   oc login https://api.xxx:6443
   ```

5. ログインしたら下記コマンドで自分専用のproject を作成してください。他とproject名が重複するとエラーになりますので、打ち間違いがないか確認してください。

   ```
   oc new-project ユーザー名-app
   ```

   ex. oc new-project user01-app

   

6. プロジェクトを作成したら下記を入力し、正しいprojectにいるか確認してください。

   ```
   oc project
   ```

7. デプロイの準備を行っていきます。下記を入力し、build設定を作成します。

  ```
  APP_NAME=quarkus-app
  oc new-build --strategy=docker --binary=true --name=$APP_NAME
  ```

  下記のようにsuccessと出れば成功です。

  ![](images/app_4.png)

8. 下記コマンドを実行してビルドを開始します。このコマンドでは作成したビルド設定に対し自分の作業ディレクトリを指定しています。このようにすると作業中のディレクトリをopenshfitに転送してビルドすることができます。—followオプションを付けているのでbuild実行中のログが流れます。ビルドには時間がかかりますのでしばらくお待ちください。

   ```
   oc start-build $APP_NAME --from-dir=/projects/sample-quarkus-0.20.0 --follow
   ```

   下記のように「push successful」と出れば成功です。

   ![](images/app_5.png)

9. ビルドしたイメージを元にアプリケーションを作成します。

   ```
   oc new-app $APP_NAME
   ```

   下記のように表示されれば作成成功です。

   ![](images/app_6.png)

10. サービス公開設定をします。

   ```
   oc expose service $APP_NAME
   ```

11. エンドポイントの確認をします。

    ```
    oc get route
    ```

    Locationの箇所にあるURLがサービスエンドポイントになります。URLにアクセスしてみてください。

    ![](images/app_7.png)

12. アクセスして下記のような画面が表示されればデプロイ成功です。

    ![](images/app_8.png)



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

2. Devfileが開きます。memoryLimit を見ると 「512Mi」となっています。「1024Mi」とすると1GiBに変更になります。変更すると自動的にsaveが始まります。右上にsucceccfulと出たら完了です。そのまま Runすると1GiBでWorkspaceが開始されます。

   ![](images/tips_2.png)


