# タイムテーブル
Red Hat OpenShift Container Platform 4 ワークショップ

|Time|Agenda|Content|
|:---:|:---|:---|
|12:00-13:00|会場準備||
|13:00-13:10|<ハンズオン>|ハンズオン環境説明|
|13:10-13:30|<講義> ハンズオン前半について|PodとConfigMapとSecret <br/>様々なデプロイメント手法<br/>CodeReady WorkSpace<br/>Quarkus|
|13:30-15:00|<ハンズオン> 前半|Lab3. 複数コンテナの連携 <br/>Lab4. 様々なデプロイメント手法<br/>Lab5. CodeReady WorkSpace|
|15:00-15:15|Break||
|15:15-16:00|<講義> ハンズオン後半|Jenkins <br>Tekton|
|16:00-17:00|<ハンズオン> 後半 <br>|Lab5. CodeReady WorkSpaceを利用した開発体験 <br>Lab6. Jenkinsベースのビルドパイプラインへの組み込み <br>Lab7. Tektonを使ったパイプラインの構築|
|17:00-17:15|QA / クロージング / アンケート||

# 諸連絡

講師より本日のWorkshop情報が記載されたetherpadへのURLをご連絡します。

# ハンズオン環境および前提

本ハンズオンは，構築済みのOpenShift(OCP)クラスターを参加者全員で共有して作業を進めます。

OCPクラスターに対するCLI操作を行う際は，構築済み踏み台サーバーにsshログインして行うか、またはCodeReady Workspaceにユーザー登録して頂き、作成した開発環境から行います。GUI操作は，クライアントPCのブラウザ(**Chrome/Firefox推奨**)を使用します。

CodeReady Workspaceを使用する場合は [Setup Workspace](setup_workspace) をご確認の上、ご自身の開発環境をセットアップしてください。CodeReady WorkspaceのURLは別途講師よりご連絡致します。

# ハンズオン概要
本ハンズオンは，OpenShift4(以降，OCPまたはOCP4)の開発編です。

以下を学びます。
- 複数コンテナの連携
- 様々なデプロイメント手法(Blue/Green、カナリアリリースなど)
- CodeReady Workspaceによる開発とデプロイ
- Jenkinsベースのビルドパイプラインへの組込
- Tektonを使ったパイプラインの構築

### 前半 
- [Lab3: 複数コンテナの連携](Lab3)
- [Lab4: 様々なデプロイメント手法](Lab4)
- [Lab5: CodeReady Workspaceを利用した開発体験](Lab5)

### 後半
- [Lab6: Jenkinsベースのビルドパイプラインへの組込](Lab6)
- [Lab7: Tektonを使ったパイプラインの構築](Lab7)
