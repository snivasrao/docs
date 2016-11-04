---

copyright:
  years: 2014, 2016

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}

# {{site.data.keyword.objectstorageshort}} のトラブルシューティング
{: #troubleshooting}

*最終更新日: 2016 年 10 月 19 日*
{: .last-updated}

{{site.data.keyword.objectstoragefull}} の使用時のトラブルシューティングに関する一般的な質問について、以下に回答を示します。
{: shortdesc}

## Liberty プロファイルで openstack4J を使用しているときに、認識されないトークン・コンテンツ・パックが返された
{: #unrecognized_token}


Liberty プロファイルで openstack4j を使用しているときに、以下のスタック・トレースが発生した可能性があります。
```
Exception thrown by application class 'org.openstack4j.connectors.okhttp.HttpResponseImpl.readEntity:124'
org.openstack4j.api.exceptions.ClientResponseException: Unrecognized token 'contentpack': was expecting ('true', 'false' or 'null') at [Source: contentpack ; line: 1, column: 12]
at org.openstack4j.connectors.okhttp.HttpResponseImpl.readEntity(HttpResponseImpl.java:124)
at org.openstack4j.core.transport.HttpEntityHandler.handle(HttpEntityHandler.java:56)
at org.openstack4j.connectors.okhttp.HttpResponseImpl.getEntity(HttpResponseImpl.java:68)
at org.openstack4j.openstack.internal.BaseOpenStackService$Invocation.execute(BaseOpenStackService.java:169)
at org.openstack4j.openstack.internal.BaseOpenStackService$Invocation.execute(BaseOpenStackService.java:163)
at org.openstack4j.openstack.storage.object.internal.ObjectStorageContainerServiceImpl.list(ObjectStorageContainerServiceImpl.java:41)
at com.mimotic.SecureMessageApp.HelloResource.getInformation(HelloResource.java:47)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
at java.lang.reflect.Method.invoke(Unknown Source)
```
{: screen}
{: tsSymptoms}


この問題は、クラス・ロードに原因があります。この場合、openstack4j ライブラリーに、Liberty プロファイルで提供されるものと同じパッケージがいくつか含まれています。例えば、OpenStack4j では JERSEY を使用しますが、これは Wink ライブラリーと衝突する可能性があります。
{: tsCauses}


この問題は、以下のいずれかによって解決できます。
{: tsResolve}
  * 逆方向のクラス・ロード (parentLast) を使用します。
  * 有効にされたフィーチャーから jaxr を除外します。


## {{site.data.keyword.objectstorageshort}} に関するヘルプおよびサポートの入手
{: #gettinghelp}

{{site.data.keyword.objectstoragefull}} の使用時に問題や疑問が生じた場合は、フォーラムを通じて情報を検索したり、質問をしたりすることにより、支援を得ることができます。サポート・チケットをオープンすることもできます。

フォーラムを使用して質問を行う場合は、{{site.data.keyword.Bluemix_notm}} 開発チームの目にとまるように、質問にタグを付けてください。

* {{site.data.keyword.objectstorageshort}} に関する技術的な質問がある場合は、[Stack Overflow](http://stackoverflow.com/search?q=object-storage+ibm-bluemix){: new_window} に質問を投稿し、質問に「ibm-bluemix」と「object-storage」のタグを付けてください。
* サービスと使用開始の手順に関する質問の場合は、[IBM developerWorks dW Answers](https://developer.ibm.com/answers/topics/objectstorage/?smartspace=bluemix){: new_window} フォーラムを使用してください。「objectstorage」と「bluemix」のタグを含めてください。

フォーラムの使用について詳しくは、[「ヘルプの取得」](https://console.ng.bluemix.net/docs/support/index.html#getting-help){: new_window}を参照してください。

IBM サポート・チケットのオープン、またはサポート・レベルとチケットの重大度に関する情報は、[「サポートへのお問い合わせ」](https://console.ng.bluemix.net/docs/support/index.html#contacting-support){: new_window}を参照してください。
