# タイムテーブル
Red Hat OpenShift Container Platform 4 ワークショップ

|Time|Agenda|Content|
|:---:|:---|:---|
|13:00-13:30|会場準備||
|13:30-13:45|<ハンズオン>|ハンズオン環境説明|
|13:45-14:15|<講義> OCP4 概要|OCP4概要とアプリケーションデプロイ|
|14:15-14:30|<デモ> |OCP4 コンソールツアー |
|14:30-15:30|<ハンズオン> 前半|Lab1. コンテナイメージのビルド&デプロイ<br/>Lab3. 複数コンテナの連携 <br/>Lab4. 様々なデプロイメント手法|
|15:30-15:45|Break||
|15:45-16:00|<講義> CodeReady Workspace|開発とデプロイ <br>|
|16:00-17:00|<ハンズオン> 後半 <br>|Lab5. CodeReady WorkSpaceを利用した開発体験 <br>Lab6. Jenkinsベースのビルドパイプラインへの組み込み <br>Lab7. Tektonを使ったパイプラインの構築|
|17:00-17:15|QA / クロージング / アンケート||

# 諸連絡
OCPクラスター接続情報など (Etherpad) ==> http://etherpad-etherpad.apps.dev.ocp41.nosue.mobi/p/workshop-20190903

# ハンズオン環境および前提
本ハンズオンは，構築済みのOpenShift(OCP)クラスターを参加者全員で共有して作業を進めます。

OCPクラスターに対するCLI操作を行う際は，CodeReady Workspaceにユーザー登録して頂き、作成した開発環境から行います。GUI操作は，クライアントPCのブラウザ(**Chrome/Firefox推奨**)を使用します。

[Setup Workspace](setup_workspace) をご確認の上、ご自身の開発環境をセットアップしてください。本日のCodeReady WorkspaceのURLは下記になります。

http://codeready-codeready.apps.dev.ocp41.nosue.mobi

# ハンズオン概要
本ハンズオンは，OpenShift4(以降，OCPまたはOCP4)の開発編です。

以下を学びます。
- アプリケーションのデプロイ
  - ソースコードからコンテナイメージをビルドしてデプロイ
  - 既存のコンテナイメージをOCP上に展開してデプロイ
- 複数コンテナの連携
- 様々なデプロイメント手法(Blue/Green、カナリアリリースなど)
- CodeReady Workspaceによる開発とデプロイ
- Jenkinsベースのビルドパイプラインへの組込
- Tektonを使ったパイプラインの構築

### 前半 
- [Lab1: コンテナイメージのビルド&デプロイ](Lab1)
- [Lab3: 複数コンテナの連携](Lab3)
- [Lab4: 様々なデプロイメント手法](Lab4)

### 後半
- [Lab5: CodeReady Workspaceを利用した開発体験](Lab5)
- [Lab6: Jenkinsベースのビルドパイプラインへの組込](Lab6)
- [Lab7: Tektonを使ったパイプラインの構築](Lab7)
