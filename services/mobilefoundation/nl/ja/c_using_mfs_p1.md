---

copyright:
  years: 2016
lastupdated:  "2016-09-12"
---

#	「開発者」プランの使用
{: #using_mobilefoundation_p1}

<!--Last updated: 12 September 2016
{: .last-updated}-->

「{{site.data.keyword.mobilefoundation_short}}: 開発者」サービス・インスタンスを作成した後、数秒後には {{site.data.keyword.Bluemix_notm}} の`「概要」`ページにアクセスでき、ここで {{site.data.keyword.mobilefoundation_short}} サービスを使い始める上で役立つチュートリアルやビデオが提供されます。

## {{site.data.keyword.mobilefirst}} サーバーの始動
{: #start_mobilefoundation_p1}
* {{site.data.keyword.mfserver_short_notm}} をデフォルト設定で始動するには、**「基本サーバーの始動」**をクリックしてください。

この選択により、以下の設定で {{site.data.keyword.mfserver_long_notm}} がプロビジョンされます。
*	1 GB のメモリー。開発アクティビティー、簡単なテスト・アクティビティー、および小規模な実動ワークロードには、このサイズで十分です。

*	`ユーザー名` と `パスワード` は自動的に生成されます。サーバーの稼働中にこれらにアクセスできます。

プロビジョン・プロセスが始動します。このプロセスには約 10 分かかり、メッセージ・ウィンドウにはこの操作の進行が示されます。完了すると、以下のことを確認できるダッシュボードが表示されます。
*	実行中のサーバーの状況 (状態、サイズ)。

*	自動的に作成されたサーバーの経路。{{site.data.keyword.mfserver_short_notm}} に接続するには、モバイル・アプリケーションでこの経路を使用します。

*	{{site.data.keyword.mfp_oc_short_notm}} にアクセスするための個人の`ユーザー名` と`パスワード`。`パスワード` は非表示です。表示するには**「パスワードの表示」**アイコンをクリックします。

*	**「コンソールの起動」**をクリックして {{site.data.keyword.mfp_oc_short_notm}} を起動します。


<!--This console runs inside the container.--> このコンソールを使用して、モバイル・アプリとモバイル・デバイスの管理、モバイル・バックエンドとしてのサーバーの使用、プッシュ通知の送信などを行えます。

##  Mobile Analytics サーバーの追加
{: #adding_analytics_server_dev}

 Mobile Analytics サーバーを {{site.data.keyword.mobilefoundation_short}} サービス・インスタンスに追加することにより、{{site.data.keyword.mobilefirst}} サーバー上のモバイル・アプリケーションをモニターできるようになりました。「開発者」プランでは、単一のノードと 1 GB メモリーを持つコンテナー・グループに、Mobile Analytics サーバーを作成します。

* **「Analytics の追加 (Add Analytics)」**をクリックして、Mobile Analytics サーバーを {{site.data.keyword.mobilefoundation_short}} サービス・インスタンスに追加します。

プロビジョン・プロセスが始動します。このプロセスには約 10 分かかり、メッセージ・ウィンドウにはこの操作の進行が示されます。  

*  {{site.data.keyword.mfp_oc_short_notm}} から、MobileFirst Analytics コンソールを起動します。

* {{site.data.keyword.mfserver_short_notm}} と Mobile Analytics サーバーとの間で、シングル・サインオンが使用可能になります。Mobile Analytics サーバーは、{{site.data.keyword.mfserver_short_notm}} と同じ LTPA 鍵とユーザー資格情報で構成されます。{{site.data.keyword.mfp_oc_short_notm}} のログインに使用したのと同じ `username` と `password` を使用して、Mobile Analytics コンソールにログインできます。

MobileFirst Analytics について詳しくは、[MobileFirst Foundation Operational Analytics](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/analytics/){: new_window}を参照できます。

**注:** {{site.data.keyword.mobilefoundation_short}} サービス・インスタンスを削除する場合や、{{site.data.keyword.mfserver_short_notm}} を再作成しようとする場合、Mobile Analytics サーバーは削除されます。

## {{site.data.keyword.mobilefirst}} サーバーの再作成
{: #recreate_mobilefoundation_p1}

*	**「再作成」**をクリックしてサーバーを再作成します。

* このアクションは、既存のサーバーを停止し、データを削除します。モバイル・サーバー内のデータはすべて失われます。更新されたバージョンがある場合は、更新されたバージョンで新しいサーバー・インスタンスが作成されます。このアクションは、完了するまでに数分かかります。

##	拡張構成のセットアップ
{: #using_mfs_advanced_p1}

拡張設定またはカスタム設定を使用してサーバーを作成するには、`「概要」` ページの**「拡張構成を使用したサーバーの始動 (Start Server with Advanced Configuration)」**を使用します。また、**「構成 (Configuration)」**タブをクリックして、サーバー構成をカスタマイズするためにサーバー設定を更新することもできます。{{site.data.keyword.mobilefoundation_short}} では、拡張設定にアクセスできます。

*	**「トポロジー (Topology)」**タブから、サーバーのサイズと、必要なインスタンスの数を選択できます。開発と中程度のテストにはデフォルトの 1 GB のサーバーで十分です。

  - ニーズに応じて、ご使用のサーバーに合ったサイズを選択してください。

* **「ノード (Nodes)」**は作成されたノード数を表示します。このフィールドは「{{site.data.keyword.mobilefoundation_short}}: 開発者」では編集できません。「開発者」プランで、ノードの数<!--in your {{site.data.keyword.IBM_notm}} container group-->は、**1** にデフォルト設定されます。

詳しくは、[{{site.data.keyword.mobilefoundation_long}} の資料](https://www.ibm.com/support/knowledgecenter/SSHS8R_8.0.0/wl_welcome.html){: new_window}を参照してください。
