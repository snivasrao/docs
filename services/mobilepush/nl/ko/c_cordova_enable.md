---

copyright:
 years: 2015, 2016

---

# 푸시 알림을 수신하도록 Cordova 애플리케이션 설정
{: #cordova_enable}
마지막 업데이트 날짜: 2016년 10월 17일
{: .last-updated}

Cordova는 JavaScript, CSS 및 HTML을 사용하여 하이브리드 애플리케이션을 빌드하는 플랫폼입니다. {{site.data.keyword.mobilepushshort}}는 Cordova 기반 iOS 및 Android 애플리케이션의 개발을 지원합니다. 

Cordova 애플리케이션에서 사용자 디바이스에 푸시 알림을 수신하도록 설정할 수 있습니다. 

## Cordova 푸시 플러그인 설치
{: #cordova_install}

클라이언트 푸시 플러그인을 설치하고 이를 사용하여 추가적으로 Cordova 애플리케이션을 개발할 수 있습니다. 이때 Bluemix에 대한 사용자 연결을 초기화하는 Cordova 코어 플러그인도 설치됩니다. 

### 시작하기 전에

1. 최신 Android Studio SDK 및 Xcode 버전을 다운로드하십시오. 
1. 에뮬레이터를 설정하십시오. Android Studio의 경우 Google Play API를 지원하는 에뮬레이터를 사용하십시오.
1. Git 명령행 도구를 설치하십시오. Windows의 경우 **Window 명령 프롬프트에서 Git 실행** 옵션을 선택하십시오. 이 도구의 다운로드 및 설치에 대한 정보는 [Git](https://git-scm.com/downloads)을 참조하십시오.
1. Node.js 및 NPM(Node Package Manager) 도구를 설치하십시오. NPM 명령행 도구는 Node.js와 함께 번들로 제공됩니다. Node.js를 다운로드하고 설치하는 방법에 대한 정보는 [Node.js](https://nodejs.org/en/download/)를 참조하십시오. 
1. 명령행에서 **npm install -g cordova** 명령을 사용하여 Cordova 명령행 도구를 설치하십시오. Cordova 푸시 플러그인을 사용하려면 필요합니다. Cordova 설치 및 Cordova 앱 설정 정보를 보려면 [Cordova Apache](https://cordova.apache.org/#getstarted)를 참조하십시오. 자세한 정보는 Cordova 푸시 플러그인 [Readme 파일](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-cordova-plugin-push)을 참조하십시오. 
1. Cordova 앱을 작성하려는 폴더로 변경하고 다음 명령을 실행하여 Cordova 애플리케이션을 작성하십시오. 기존의 Cordova 앱이 있을 경우 3단계로 가십시오. 
```
cordova create your_app_name
cd your_app_name
```
	{: codeblock}
- 선택사항: **config.xml** 파일을 편집하고 <name> 요소의 애플리케이션 이름을 기본값인 HelloCordova 이름이 아닌 사용자가 선택하는 이름으로 변경할 수 있습니다. 

올바른 번들 ID를 지정했는지 확인하십시오. 올바르지 않은 번들 ID를 지정하면 Xcode에 다음 오류 메시지가 표시됩니다. 

* 실행 파일이 올바르지 않은 인타이틀먼트로 서명되었습니다.
* 애플리케이션의 코드 서명 인타이틀먼트에 지정된 인타이틀먼트가 프로비저닝 프로파일에 지정된 인타이틀먼트와 일치하지 않습니다. 이 문제를 수정하려면 Xcode 또는 Cordova 앱 **config.xml** 파일에서 올바른 번들 ID를 지정하십시오. 

1. Corova 애플리케이션의 config.xml 파일에 최소 지원 API 또는 배치 대상 선언을 추가하십시오. minSdkVersion 값은 15보다 커야 합니다. targetSdkVersion 값은 항상 Google을 통해 제공받을 수 있는 최신 Android SDK를 반영해야 합니다. 
	
	* Android - 편집기에서 config.xml 파일을 열고 최소의 대상 SDK 버전으로
`<platform name="android">` 요소를 업데이트하십시오. 

```
< !-- add deployment target declaration -->
add deployment target declaration <preference name="android-minSdkVersion" value="15" />
  <preference name="android-targetSdkVersion" value="23" />
</platform>
```
    {: codeblock}

   * iOS - 배치 대상 선언으로 <platform name="ios"> 요소를 업데이트하십시오. 

```
<platform name ="ios">
<preference name=deployment-target" value="8.0" /> <!-- other properties -->
</ platform>
```
	{: codeblock}

1. Cordova 명령행 인터페이스(CLI)에서 다음 명령을 사용하여 플랫폼(iOS, Android 또는 둘 다)을 추가하십시오. 
```
cordova platform add ios
	cordova platform add android
	```
	{: codeblock}

1. Cordova 애플리케이션 루트 디렉토리에서 다음 명령을 입력하여 Cordova 푸시 플러그인을 설치하십시오. **cordova plugin add ibm-mfp-push**추가한 플랫폼에 따라 다음과 같이 표시됩니다. 
```
Installing "ibm-mfp-push" for android
	Installing "ibm-mfp-push" for ios
	```
	{: codeblock}

1. *your-app-root-folder*에서 다음 명령을 사용하여 Cordova 코어 플러그인과 푸시 플러그인이 정상적으로 설치되었는지 확인하십시오. **cordova plugin list**.추가한 플랫폼에 따라 다음과 같이 표시됩니다. 
```
ibm-mfp-core 1.0.0 "MFPCore"
	ibm-mfp-push 1.0.0 “MFPPush"
	```
	{: codeblock}

1. (iOS만 해당) - iOS 개발 환경을 구성하십시오.
2. 다음 부속 단계를 완료하십시오.

 a. Xcode가 있는 *your-app-name***/platforms/ios** 디렉토리에서 your-app-name.xcodeproj 파일을 여십시오. 

 b. 브릿지 헤더를 추가하십시오. **빌드 설정 > Swift 컴파일러 - 코드 생성 > Objective-C 브릿지 헤더**로 이동하여 다음 경로를 추가하십시오. *your-project-name***/Plugins/ibm-mfp-core/Bridging-Header.h**

 c. 프레임워크 매개변수를 추가하십시오. **빌드 설정 > 링크 > Runpath 검색 경로**로 이동하여 `@executable_path/Frameworks` 매개변수를 추가하십시오. 

 d. 브릿지 헤더에서 다음 푸시 가져오기 명령을 주석 해제하십시오. *your-project-name***/Plugins/ibm-mfp-core/Bridging-Header.h**로 이동하십시오. 

```
//#import <IMFPush/IMFPush.h>
	//#import <IMFPush/IMFPushClient.h>
	//#import <IMFPush/IMFResponse+IMFPushCategory.h>
	```
	{: codeblock}

 e. Xcode를 사용하여 애플리케이션을 빌드하고 실행하십시오.

1. (Android만 해당) - 다음 명령을 사용하여 Android 프로젝트를 빌드하십시오.
**cordova build android**.

	**참고**: Android Studio에서 프로젝트를 열기 전에 먼저 Cordova CLI를 통해 Cordova 애플리케이션을 빌드하십시오. 그러면 빌드 오류를 방지하는 데 유용합니다. 


## Cordova 플러그인 초기화
{: #cordova_initialize}

{{site.data.keyword.mobilepushshort}} 서비스 Cordova 플러그인을 사용하려면 먼저 애플리케이션 라우트와 애플리케이션 GUID를 전달하여 플러그이니을 초기화해야 합니다. 플러그인을 초기화한 후 Bluemix 대시보드에서 작성한 서버 앱에 연결할 수 있습니다. Cordova 플러그인은 Cordova 앱이 Bluemix 서비스와 통신할 수 있도록 해주는 Android 및 iOS 클라이언트 SDK용 랩퍼입니다. 

1. 다음 코드 스니펫을 복사하여 기본 JavaScript 파일(일반적으로 **www/js** 디렉토리 아래에 있음)에 붙여넣어 BMSClient를 초기화하십시오. 

```
BMSClient.initialize("https://myapp.mybluemix.net","App GUID");
```
	{: codeblock}

1. Bluemix Route 및 appGUID 매개변수를 사용하려면 코드 스니펫을 수정하십시오. 푸시 대시보드의 **모바일 옵션** 링크를 클릭하여 앱 라우트, 앱 GUID, 클라이언트 본인확인정보를 가져오십시오. `BMSClient.initialize` 코드 스니펫에서 라우트 및 앱 GUID 값을 매개변수로 사용하십시오. 

	**참고**: Cordova CLI(예: Cordova create app-name 명령)를 사용하여 Cordova 앱을 작성한 경우 이 Javascript 코드를 **index.js** 파일에서 `onDeviceReady: function()` 함수의 `app.receivedEvent` 함수 뒤에 넣어서 BMS 클라이언트를 초기화하십시오. 

```
  onDeviceReady: function() {
	 app.receivedEvent('deviceready');
BMSClient.initialize("https://myapp.mybluemix.net","App GUID");
    },
```
	{: codeblock}

## 디바이스 등록
{: #cordova_register}


디바이스를 {{site.data.keyword.mobilepushshort}} 서비스에 등록하려면 등록 메소드를 호출하십시오. 다음 코드 스니펫을 Cordova 애플리케이션에 복사하여 디바이스를 등록하십시오. 

```
var success = function(message) { console.log("Success: " + message); };
	var failure = function(message) { console.log("Error: " + message); };
	MFPPush.registerDevice({}, success, failure);
```
	{: codeblock}

### Android
{: #cordova_register_android}
Android에서는 이 설정 매개변수를 사용하지 않습니다. Android 앱만 빌드하는 경우 빈 오브젝트를 전달하십시오. 예를 들면, 다음과 같습니다. 

```
MFPPush.registerDevice({}, success, failure);
	MFPPush.unregisterDevice(success, failure);
```
	{: codeblock}

### iOS
{: #cordova_register_ios}
경보, 배지 및 사운드 특성을 사용자 정의하려면, 다음 JavaScript 코드 스니펫을 Cordova 애플리케이션의 웹 파트에 추가하십시오. 

```
var settings = {
ios: {
           alert: true,
           badge: true,
           sound: true
       }
	 }
	MFPPush.registerDevice(settings, success, failure);
```
	{: codeblock}


### JavaScript
{: #cordova_register_js}

```
MFPPush.registerDevice({}, success, failure);
```
	{: codeblock}

JSON.parse를 사용하여 Javascript에서 성공 응답 매개변수의 컨텐츠에 액세스할 수 있습니다.
**var token = JSON.parse(response).token**


사용 가능한 키는 `token`과 `deviceId`입니다. 

다음 JavaScript 코드 스니펫은 Bluemix 모바일 서비스 클라이언트 SDK를 초기화하고, 디바이스를 {{site.data.keyword.mobilepushshort}} 서비스에 등록하고, 푸시 알림을 청취하는 방법을 표시합니다. 이 코드를 Javascript 파일에 포함하십시오. 

```
//Register device token with Bluemix Push Notification Service
funcapplication(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData){
  CDVMFPPush.sharedInstance().didRegisterForRemoteNotifications(deviceToken)
}
```
	{: codeblock}

```
//Handle error when failed to register device token with APNs
funcapplication(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSErrorPointer){
CDVMFPPush.sharedInstance().didFailToRegisterForRemoteNotifications(error)
}
```
	{: codeblock}
**onDeviceReady: function()**에서 다음을 수행하십시오.

```
  onDeviceReady: function() {
	 app.receivedEvent('deviceready');
  BMSClient.initialize("https://http://myroute_mybluemix.net","my_appGuid");
  var success = function(message) { console.log("Success: " + message); };
  var failure = function(message) { console.log("Error: " + message); };
  var settings = {
       ios: {
           alert: true,
           badge: true,
           sound: true
       }
	   };
   MFPPush.registerDevice(settings, success, failure);
   var notification = function(notif){
       alert (notif.message);
    };
    MFPPush.registerNotificationsCallback(notification);
	 }
```
	{: codeblock}

### Objective-C
{: #cordova_register_objective}
다음의 Objective-C 코드 스니펫을 애플리케이션 위임 클래스에 추가하십시오. 

```
// Register the device token with Bluemix Push Notification Service
	- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
	  [[CDVMFPPush sharedInstance] didRegisterForRemoteNotifications:deviceToken];
	}
	// Handle error when failed to register device token with APNs
	- (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error {
	   [[CDVMFPPush sharedInstance] didFailToRegisterForRemoteNotificationsWithError:error];
	}
```
	{: codeblock}

###Swift
{: #cordova_register_swift}
다음의 Swift 코드 스니펫을 애플리케이션 위임 클래스에 추가하십시오. 

```
funcapplication(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData){
   CDVMFPPush.sharedInstance().didRegisterForRemoteNotifications(deviceToken)
}
// Handle error when failed to register device token with APNs
funcapplication(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSErrorPointer){
   CDVMFPPush.sharedInstance().didFailToRegisterForRemoteNotifications(error)
}
```
	{: codeblock}

##다음 단계

{: #cordova_register_next}

프로젝트를 빌드하고 다음 명령을 사용하여 프로젝트를 실행하십시오. 

####Android
{: android-next-steps}

```
cordova build android
```
	{: codeblock}

```
cordova run android
```
	{: codeblock}

####iOS
{: ios-next-steps}

```
cordova build ios
```
	{: codeblock}

```
cordova run ios
```
	{: codeblock}

## 디바이스에서 푸시 알림 수신
{: #cordova_receive}

디바이스에서 푸시 알림을 받으려면 다음 코드 스니펫을 복사하십시오. 

###JavaScript

다음의 JavaScript 코드 스니펫을 Cordova 애플리케이션의 웹 파트에 추가하십시오. 
```
var notification = function(notification){
    // notification is a JSON object.
    alert(notification.message);
};
MFPPush.registerNotificationsCallback(notification);
```
	{: codeblock}

###Android 알림 특성

다음 섹션에는 Android 알림 특성이 나열되어 있습니다. 

* 메시지 - 푸시 알림 메시지
* 페이로드 - 알림 페이로드를 포함하는 JSON 오브젝트


###iOS 알림 특성

다음 섹션에는 iOS 알림 특성이 나열되어 있습니다. 

* 메시지 - 푸시 알림 메시지
* 페이로드 - 알림 페이로드를 포함한 JSON 오브젝트
action-loc-key - 이 문자열은 `View` 대신 해당 단추 제목에 사용할 현재 로컬라이제이션의 현지화된 문자열을 가져올 키로 사용됩니다. 
* 배지 - 앱 아이콘의 배지로 표시할 숫자입니다. 이 특성을 비워두면 배지가 변경되지 않습니다. 배지를 제거하려면 이 특성의 값을 0으로 설정하십시오. 
* 사운드 - 앱 번들 또는 앱 데이터 컨테이너의 라이브러리/사운드 폴더에 있는 사운드 파일의 이름입니다. 

###Objective-C

다음의 Objective-C 코드 스니펫을 애플리케이션 위임 클래스에 추가하십시오. 

```
// Handle receiving a remote notification
-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {

 [[CDVMFPPush sharedInstance] didReceiveRemoteNotification:userInfo];
}
```
	{: codeblock}


```
// Handle receiving a remote notification on launch
		- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    [[CDVMFPPush sharedInstance] didReceiveRemoteNotificationOnLaunch:launchOptions];
	}
```
	{: codeblock}

###Swift

다음의 Swift 코드 스니펫을 애플리케이션 위임 클래스에 추가하십시오. 
```
// Handle receiving a remote notification
funcapplication(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: ){
  CDVMFPPush.sharedInstance().didReceiveRemoteNotification(userInfo)
}
```
	{: codeblock}

```
// Handle receiving a remote notification on launch
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
  {
  CDVMFPPush.sharedInstance().didReceiveRemoteNotificationOnLaunch(launchOptions)
 }
```
	{: codeblock}

## 기본 푸시 알림 전송
{: #push-send-notifications}

애플리케이션을 개발한 후에는 기본 푸시 알림을 전송할 수 있습니다. 

기본 푸시 알림을 전송하려면 다음 단계를 완료하십시오. 

1. **알림 전송**을 선택하고 **받는 사람** 옵션을 선택하여 메시지를 작성하십시오. 지원되는 옵션은 **태그별 디바이스**, **디바이스 ID**, **사용자 ID**, **Android 디바이스**, **iOS 디바이스**, **웹 알림** 및 **모든 디바이스**입니다.
**참고**: **모든 디바이스** 옵션을 선택하는 경우 {{site.data.keyword.mobilepushshort}}을 구독하는 모든 디바이스가 알림을 수신합니다.
![알림 화면](images/tag_notification.jpg)

2. **메시지** 필드에 메시지를 작성하십시오. 필요에 따라 선택적 옵션을 구성하도록 선택하십시오.
3. **전송**을 클릭하십시오. 
3. 디바이스가 알림을 수신했는지 확인하십시오. 

다음 스크린샷은 Android 디바이스와 iOS 디바이스의 포그라운드에서 {{site.data.keyword.mobilepushshort}}을 처리하는 경보 상자를 표시합니다. 

![Android의 포그라운드 푸시 알림](images/Android_Screenshot.jpg)

![iOS의 포그라운드 푸시 알림](images/iOS_Screenshot.jpg)

   다음 이미지는 Android에서 사용되는 백그라운드의 {{site.data.keyword.mobilepushshort}}을 표시합니다.
![Android의 백그라운드 푸시 알림](images/background.jpg)

## 다음 단계
{: #next_steps_tags}

정상적으로 기본 알림을 설정한 후 태그 기반 알림 및 고급 옵션을 구성할 수 있습니다. 

{{site.data.keyword.mobilepushshort}} 서비스 기능을 앱에 추가하십시오.
태그 기반 알림을 사용하려면 [태그 기반 알림](c_tag_basednotifications.html)을 참조하십시오.
고급 알림 옵션을 사용하려면 [고급 푸시 알림 사용](t_advance_badge_sound_payload.html)의 내용을 참조하십시오. 
