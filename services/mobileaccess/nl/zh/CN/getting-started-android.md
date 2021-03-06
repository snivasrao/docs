---

copyright:
  years: 2015, 2016
lastupdated: "2016-10-10"
---
{:shortdesc: .shortdesc}
{:screen:.screen}


# 设置 Android SDK
{: #getting-started-android}

在 Android 应用程序中安装 {{site.data.keyword.amafull}} 客户端 SDK，初始化该 SDK，然后对受保护和不受保护的资源发起请求。


{:shortdesc}

## 开始之前
{: #before-you-begin}
您必须具有：
* 受 {{site.data.keyword.amashort}} 服务保护的 {{site.data.keyword.Bluemix_notm}} 应用程序实例。有关如何创建 {{site.data.keyword.Bluemix_notm}} 后端应用程序的更多信息，请参阅[入门](index.html)。
* 服务参数值。在 {{site.data.keyword.Bluemix_notm}}“仪表板”中打开服务。单击**移动选项**。`applicationRoute` 和 `tenantId`（也称为 `appGUID`）值会显示在**路由**和**应用程序 GUID/TenantId** 字段中。您将需要这些值来初始化 SDK，并将请求发送到后端应用程序。
* Android Studio 项目，设置为使用 Gradle。有关如何设置 Android 开发环境的更多信息，请参阅 [Google Developer Tools](http://developer.android.com/sdk/index.html)。

## 安装 {{site.data.keyword.amashort}} 客户端 SDK
{: #install-mca-sdk}

{{site.data.keyword.amashort}} 客户端 SDK 通过 Gradle 进行分发；Gradle 是用于 Android 项目的依赖关系管理器。Gradle 会自动从存储库下载工件，并将其提供给 Android 应用程序。

1. 创建 Android Studio 项目或打开现有项目。

1. 打开应用程序的 `build.gradle` 文件（**非** 项目的 `build.gradle` 文件）。

1. 找到 `build.gradle` 文件的 **dependencies** 部分。添加 {{site.data.keyword.amashort}} 客户端 SDK 的编译依赖关系：

	```Gradle
	dependencies {
		compile group: 'com.ibm.mobilefirstplatform.clientsdk.android',    
        name:'core',
        version: '2.+',
        ext: 'aar',
        transitive: true
    	// other dependencies  
}
```

1. 使用 Gradle 同步项目。单击**工具 &gt; Android &gt; 使用 Gradle 文件同步项目**。

1. 打开 Android 项目的 `AndroidManifest.xml` 文件。
在 `<manifest>` 元素下添加因特网访问许可权：

	```XML
	<uses-permission android:name="android.permission.INTERNET" />
```

## 初始化 {{site.data.keyword.amashort}} 客户端 SDK
{: #initalize-mca-sdk}

通过将 **context** 和 **region** 传递到 `initialize` 方法来初始化客户端 SDK。在 Android 应用程序中，通常会将初始化代码放置在主 Activity 的 `onCreate` 方法中，但这不是强制性的。

```Java
  BMSClient.getInstance().initialize(getApplicationContext(), BMSClient.REGION_UK);
					
  BMSClient.getInstance().setAuthorizationManager(
                 MCAAuthorizationManager.createInstance(this, "MCAServiceTenantId"));

```

   * 将 `BMSClient.REGION_UK` 替换为相应的区域。

要查看 {{site.data.keyword.Bluemix_notm}} 区域，请单击菜单栏中的**头像**图标 ![“头像”图标](images/face.jpg "“头像”图标")，以打开**帐户和支持**窗口小部件。区域值应该为以下其中一个值：`BMSClient.REGION_US_SOUTH`、`BMSClient.REGION_SYDNEY` 或 `BMSClient.REGION_UK`。
   * 将“MCAServiceTenantId”替换为 **tenantId** 值（请参阅[开始之前](#before-you-begin)）。 

## 对移动后端应用程序发起请求
{: #request}

初始化 {{site.data.keyword.amashort}} 客户端 SDK 后，可以开始对移动后端应用程序发起请求。

1. 尝试对新移动后端应用程序的受保护端点发送请求。在浏览器中，打开以下 URL：`{applicationRoute}/protected`（例如，`http://my-mobile-backend.mybluemix.net/protected`）。有关获取 `{applicationRoute}` 值的信息，请参阅[开始之前](#before-you-begin)。 
	
	使用 MobileFirst Services Starter 样板创建的移动后端应用程序的 `/protected` 端点通过 {{site.data.keyword.amashort}} 进行保护。由于此端点只能由安装了 {{site.data.keyword.amashort}} 客户端 SDK 的移动应用程序进行访问，因此会在浏览器中返回 `Unauthorized` 消息。



1. 使用 Android 应用程序对同一端点发起请求。初始化 `BMSClient` 后，添加以下代码：

	```Java
	Request request = new Request("/protected", Request.GET);
	request.send(this, new ResponseListener() {
		@Override
		public void onSuccess (Response response) {
			Log.d("Myapp", "onSuccess :: " + response.getResponseText());
		}
		@Override
		public void onFailure (Response response, Throwable t, JSONObject extendedInfo) {
			if (null != t) {
				Log.d("Myapp", "onFailure :: " + t.getMessage());
			} else if (null != extendedInfo) {
				Log.d("Myapp", "onFailure :: " + extendedInfo.toString());
			} else {
				Log.d("Myapp", "onFailure :: " + response.getResponseText());
			}
		}
	});
	```

1. 请求成功后，将在 LogCat 实用程序中看到以下输出：


	![图像](images/getting-started-android-success.png)

## 后续步骤
{: #next-steps}

连接到受保护端点时，无需任何凭证。如果需要用户登录到您的应用程序，那么必须配置 Facebook、Google 或定制认证。
* [Facebook](facebook-auth-android.html)
* [Google](google-auth-android.html)
* [定制](custom-auth-android.html)
