---

copyright:
  years: 2015, 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Java für Anwendungsentwickler
{: #java}

Letzte Aktualisierung: 7. September 2016
{: .last-updated}

Verwenden Sie Java, um Anwendungen zu erstellen und anzupassen, die in {{site.data.keyword.iot_full}} mit Ihrer Organisation interagieren. Verwenden Sie die bereitgestellten Informationen und Beispiele, um mit der Entwicklung von Anwendungen mithilfe von Java zu beginnen.
{:shortdesc}

## Java-Client und Ressourcen herunterladen
{: #java_client_download}

Wechseln Sie für den Zugriff auf die Java-Clientbibliotheken und Beispiele für {{site.data.keyword.iot_short_notm}} in das Repository [iot-java](https://github.com/ibm-watson-iot/iot-java) in GitHub und folgen Sie den Installationsanweisungen.


## Konstruktor
{: #constructor}

Der Konstruktor erstellt die Clientinstanz und akzeptiert ein `Properties`-Objekt, das folgende Definitionen enthält:

| Definition     |Beschreibung     |
|----------------|----------------|
|`org` |Die ID Ihrer Organisation. Dies ist ein erforderlicher Wert. Wenn Sie einen Quickstart-Ablauf verwenden, geben Sie `quickstart` an.|
|`id` |Die eindeutige ID der Anwendung innerhalb Ihrer Organisation.|
|`auth-method`  |Die Authentifizierungsmethode, deren einziger aktuell unterstützter Wert `apikey` lautet.|
|`auth-key`   |Ein optionaler API-Schlüssel, der erforderlich ist, wenn für 'auth-method' die Einstellung `apikey` festgelegt ist.  |
|`auth-token`   |Ein API-Schlüsseltoken, das erforderlich ist, wenn für 'auth-method' die Einstellung `apikey` festgelegt ist. |
|`clean-session`|Der Wert 'true' oder 'false', der nur erforderlich ist, wenn Sie die Anwendung im Modus der permanenten Subskription verbinden möchten. Die Standardeinstellung für `clean-session` lautet `true`.|
|`shared-subscription`|Ein boolescher Wert. Festgelegt ist `true`, wenn Sie skalierbare Anwendungen erstellen möchten, die die Nachrichtenlast gleichmäßig auf mehrere Instanzen der Anwendung aufteilen. Weitere Informationen finden Sie in [Skalierbare Anwendungen](mqtt.html#/scalable-applications#scalable-applications).
Das `Properties`-Objekt erstellt Definitionen, die für die Interaktion mit dem {{site.data.keyword.iot_short_notm}}-Modul verwendet werden. Wenn Sie die Eigenschaften für dieses Objekt nicht angeben oder wenn Sie `quickstart` angeben, wird der Client als nicht registriertes Gerät mit dem {{site.data.keyword.iot_short_notm}}-Quickstart-Service verbunden. 

Das folgende Codebeispiel zeigt, wie die Anwendungsclientinstanz im Modus `quickstart` erstellt werden kann:

```
    import com.ibm.iotf.client.app.ApplicationClient;
    import java.util.Properties;

    Properties options = new Properties();
    options.put("org", "quickstart");

    ApplicationClient myClient = new ApplicationClient(options);
```

Das folgende Codebeispiel zeigt, wie die Anwendungsclientinstanz im Modus 'registrierter Ablauf' erstellt werden kann:

```
    Properties options = new Properties();
    options.put("org", "uguhsp");
    options.put("id", "app" + (Math.random() * 10000));
    options.put("Authentication-Method","apikey");
    options.put("API-Key", "<API-Schlüssel>");
    options.put("Authentication-Token", "<Authentifizierungstoken>");

    ApplicationClient myClient = new ApplicationClient(options);
```

### Konfigurationsdatei verwenden

Statt ein `Properties`-Objekt direkt einzuschließen, können Sie eine Konfigurationsdatei verwenden, die die Name/Wert-Paare für das `Properties`-Objekt im folgenden Format enthält: 

```
    Properties props = ApplicationClient.parsePropertiesFile(new File("C:\\temp\\application.prop"));
    ApplicationClient myClient = new ApplicationClient(props);
    ...
```
Die Anwendungskonfigurationsdatei muss das folgende Format aufweisen:
```
    [application]
    org=$orgId
    id=$myApplication
    auth-method=apikey
    auth-key=$key
    auth-token=$token
    enable-shared-subscription=true|false
```

## Verbindung zu {{site.data.keyword.iot_short_notm}} herstellen
{: #connecting_to_iotp}

Verwenden Sie zum Herstellen einer Verbindung zu {{site.data.keyword.iot_short_notm}} die Funktion `connect()`. Die Funktion `connect()` enthält einen optionalen booleschen Parameter mit der Bezeichnung `autoRetry`, mit dem festgelegt wird, ob die Bibliothek bei Auftreten des Verbindungsfehlers 'MqttException' versucht, die Verbindung erneut herzustellen. Für `autoRetry` ist standardmäßig der Wert 'true' festgelegt. Wenn die Verbindung mit 'MqttSecurityException' fehlschlägt, da falsche Details für die Geräteregistrierung übergeben wurden, versucht die Bibliothek nicht, die Verbindung erneut herzustellen, auch wenn für `autoRetry` die Einstellung `true` festgelegt ist.

```
    Properties props = ApplicationClient.parsePropertiesFile(new File("C:\\temp\\application.prop"));
    ApplicationClient myClient = new ApplicationClient(props);

    myClient.connect();
```

Nach dem erfolgreichen Herstellen einer Verbindung zum {{site.data.keyword.iot_short_notm}}-Service können Ihre Anwendungsclients Geräteereignisse und Gerätestatus subskribieren und Geräteereignisse und Befehle publizieren.

## Geräteereignisse subskribieren
{: #subscribing_device_events}

Ereignisse sind der Mechanismus, über den Geräte Daten in {{site.data.keyword.iot_short_notm}} publizieren. Das Gerät steuert den Inhalt des Ereignisses und ordnet jedem Ereignis, das von ihm gesendet wird, einen Namen zu.

Wenn ein Ereignis von der {{site.data.keyword.iot_short_notm}}-Instanz empfangen wird, geben die Berechtigungsnachweise des empfangenen Ereignisses das sendende Gerät an; dies bedeutet, dass ein Gerät nicht die Identität eines anderen Geräts annehmen kann.


Anwendungen subskribieren standardmäßig alle Ereignisse von allen verbundenen Geräten. Steuern Sie den Bereich der Subskription mithilfe der Parameter für den Gerätetyp, die Geräte-ID, das Ereignis und das Nachrichtenformat. Folgende Codebeispiele zeigen, wie der Bereich einer Subskription mithilfe dieser Parameter definiert werden kann:

### Alle Ereignisse von allen Geräten subskribieren


```
    myClient.connect();
    myClient.subscribeToDeviceEvents();
```
### Alle Ereignisse von allen Geräten eines bestimmten Typs subskribieren


```

    myClient.connect();
    myClient.subscribeToDeviceEvents("iotsample-arduino");
```

### Alle Ereignisse von einem bestimmten Gerät subskribieren 


```

    myClient.connect();
    myClient.subscribeToDeviceEvents("iotsample-arduino", "00aabbccddee");
```
### Bestimmtes Ereignis von mindestens zwei unterschiedlichen Geräten subskribieren

```

    myClient.connect();
    myClient.subscribeToDeviceEvents("iotsample-arduino", "00aabbccddee", "myEvent");
    myClient.subscribeToDeviceEvents("iotsample-arduino", "10aabbccddee", "myEvent");
```
### Ereignisse subskribieren, die im JSON-Format publiziert wurden


```

    myClient.connect();
    myClient.subscribeToDeviceEvents("iotsample-arduino", "00aabbccddee", "myEvent", "json", 0);
```

**Hinweis**: Ein einzelner Client kann mehrere Subskriptionen unterstützen.

## Ereignisse von Geräten verarbeiten
{: #handling_device_events}

Zum Verarbeiten der Ereignisse, die über Ihre Subskriptionen empfangen werden, registrieren Sie eine Callback-Methode für Ereignisse. Die Nachrichten werden als Instanz der Ereignisklasse zurückgegeben, die folgende Parameter aufweist:

|Parameter|Datentyp|Beschreibung|
|:---|:---|
|`event.device`|Zeichenfolge|Gibt das Gerät unter allen Gerätetypen der Organisation eindeutig an.|
|`event.deviceType`|Zeichenfolge|Gibt den Gerätetyp an. In der Regel ist 'deviceType' eine Zusammenfassung von Geräten, die eine bestimmte Aufgabe ausführen, beispielsweise 'Wetterballon'.|
|`event.deviceId`|Zeichenfolge|Stellt die ID des Geräts dar. In der Regel ist 'deviceId' bei vorgegebenem Gerätetyp eine eindeutige Kennung des betreffenden Geräts, beispielsweise die Seriennummer oder MAC-Adresse.|
|`event.event`|Zeichenfolge|In der Regel zum Gruppieren bestimmter Ereignisse verwendet, beispielsweise 'Status', 'Warnung' und 'Daten'.|
|`event.format`|Zeichenfolge|Das Format kann eine beliebige Zeichenfolge sein, zum Beispiel 'JSON'.  |
|`event.data`|Wörterverzeichnis|Die Angaben für die Nachrichtennutzdaten. Die maximale Länge beträgt 131072 Byte.|
|`event.timestamp`|Datum und Uhrzeit|Datum und Uhrzeit des Ereignisses.|


Der folgende Code zeigt eine Beispielimplementierung für Ereignis-Callback:

```
  import com.ibm.iotf.client.app.Event;
  import com.ibm.iotf.client.app.EventCallback;
  import com.ibm.iotf.client.app.Command;

  import java.util.concurrent.BlockingQueue;
  import java.util.concurrent.LinkedBlockingQueue;

  /**
    * Eine Beispielklasse für Ereignis-Callback, die die Geräteereignisse in einem separaten Thread verarbeitet.
*
    */
   class MyEventCallback implements EventCallback, Runnable {

	// Eine Warteschlange, die die Ereignisse für eine reibungslose Verarbeitung von MQTT-Nachrichten enthält und handhabt
	private BlockingQueue<Ereignis> evtQueue = new LinkedBlockingQueue<Ereignis>();

	public void processEvent(Event e) {
		try {
			evtQueue.put(e);
		} catch (InterruptedException e1) {
			e1.printStackTrace();
		}
	}

	@Override
	public void processCommand(Command cmd) {
		System.out.println("Command received:: " + cmd);
	}

	@Override
	public void run() {
		while(true) {
			Event e = null;
			try {
				e = evtQueue.take();
				// In diesem Beispiel ist die einzige Ausgabe das Ereignis
				System.out.println("Event:: " + e.getDeviceId() + ":" + e.getEvent() + ":" + e.getPayload());
			} catch (InterruptedException e1) {
				// Ignorieren Sie die Ausnahmebedingung 'Interrupted', versuchen Sie es erneut
				continue;
			}
		}
	}
    }
```

Ist das Ereignis-Callback zum Anwendungsclient hinzugefügt, wird stets die Methode `processEvent()` aufgerufen, wenn ein Ereignis publiziert wird, das mit der Subskription übereinstimmt. Das folgende Snippet zeigt, wie das Ereignis-Callback der Anwendungsclientinstanz hinzugefügt wird:



```
    myClient.connect()
    myClient.setEventCallback(new MyEventCallback());
    myClient.subscribeToDeviceEvents();
```

Ähnlich wie die Anwendung Geräteereignisse subskribiert, kann sie auch Befehle subskribieren, die an die Geräte gesendet werden. Das folgende Codebeispiel zeigt, wie alle Befehle für alle Geräte der Organisation subskribiert werden:

```

    myClient.connect()
    myClient.setEventCallback(new MyEventCallback());
    myClient.subscribeToDeviceCommands();
```

Für die Steuerung der Befehlssubskription sind überladene Methoden verfügbar. Die Methode `processCommand()` wird aufgerufen, wenn an das Gerät ein Befehl gesendet wird, der mit der Befehlssubskription übereinstimmt.


## Gerätestatus subskribieren
{: #subscribing_device_status}

Anwendungen können, ähnlich wie beim Subskribieren von Geräteereignissen, auch Gerätestatus wie beispielsweise Statusdaten zum Herstellen und Trennen von Verbindungen zwischen Geräten und {{site.data.keyword.iot_short_notm}} subskribieren. Diese Subskription subskribiert standardmäßig alle Statusaktualisierungen aller verbundenen Geräte. Steuern Sie den Bereich der Subskription mithilfe der Parameter für den Gerätetyp und die Geräte-ID. Folgende Codebeispiele zeigen, wie der Bereich einer Subskription mithilfe dieser Parameter definiert werden kann:

### Statusaktualisierungen aller Geräte subskribieren

```
    myClient.connect();
    myClient.subscribeToDeviceStatus();
```

### Statusaktualisierungen aller Geräte eines bestimmten Typs subskribieren


```

    myClient.connect();
    myClient.subscribeToDeviceStatus("iotsample-arduino");
```

### Statusaktualisierungen für zwei verschiedene Geräte subskribieren


```

    myClient.connect();
    myClient.subscribeToDeviceStatus("iotsample-arduino", "00aabbccddee");
    myClient.subscribeToDeviceStatus("iotsample-arduino", "10aabbccddee");
```

**Hinweis**: Ein einzelner Client kann mehrere Subskriptionen unterstützen.

## Statusaktualisierungen von Geräten verarbeiten
{: #handling_device_status_updates}

Zum Verarbeiten der Statusaktualisierungen, die über Ihre Subskriptionen empfangen werden, müssen Sie eine Callback-Methode für Statusaktualisierungen registrieren. Für die Statusereignisse `Connect` und `Disconnect` werden die Nachrichten als Instanz der Statusklasse zurückgegeben, die folgende Parameter enthält:


| Parameter     |Datentyp     |
|----------------|----------------|
|`status.clientAddr` |Zeichenfolge|
|`status.protocol`  |Zeichenfolge|
|`status.clientId`   |Zeichenfolge|
|`status.user`   |Zeichenfolge|
|`status.time`   |java.util.Date|
|`status.action` |Zeichenfolge|
|`status.connectTime`   |java.util.Date|
|`status.port`|Ganze Zahl|

Folgende Eigenschaften werden nur festgelegt, wenn das Statusereignis `Disconnect` lautet: 

| Eigenschaft     |Datentyp     |
|----------------|----------------|
|`status.writeMsg` |Ganze Zahl|
|`status.readMsg`  |Ganze Zahl|
|`status.reason`   |Zeichenfolge|
|`status.readBytes`   |Ganze Zahl|
|`status.writeBytes`   |Ganze Zahl|


Das folgende Codebeispiel zeigt die Beispielimplementierung für den Status-Callback:

```

  private static class MyStatusCallback implements StatusCallback {

      public void processApplicationStatus(ApplicationStatus status) {
          System.out.println("Application Status = " + status.getPayload());
      }

      public void processDeviceStatus(DeviceStatus status) {
          if(status.getAction() == "Disconnect") {
              System.out.println("device: "+status.getDeviceId()
                                  + "  time: "+ status.getTime()
                                  + "  action: " + status.getAction()
                                  + "  reason: " + status.getReason());
          } else {
              System.out.println("device: "+status.getDeviceId()
                                  + "  time: "+ status.getTime()
                                  + "  action: " + status.getAction());
          }
      }
  }
```

Ist das Status-Callback zum Anwendungsclient hinzugefügt, wird stets die Methode `processDeviceStatus()` aufgerufen, wenn für ein Gerät, das mit den Kriterien übereinstimmt, eine Verbindung zu {{site.data.keyword.iot_short_notm}} hergestellt wird bzw. diese getrennt wird. Das folgende Codebeispiel zeigt, wie Sie die Callback-Instanz für den Status dem Anwendungsclient hinzufügen können. 

```

    myClient.connect()
    myClient.setStatusCallback(new MyStatusCallback());
    myClient.subscribeToDeviceStatus();
```
Anwendungen können beliebige andere Anwendungsstatus subskribieren, wie beispielsweise Statusdaten zum Herstellen und Trennen von Verbindungen zwischen Anwendungen und Watson IoT Platform. Das folgende Codesnippet zeigt, wie der Anwendungsstatus in der Organisation subskribiert wird:
```
    myClient.connect()
    myClient.setEventCallback(new MyEventCallback());
    myClient.subscribeToApplicationStatus();
```
Mit der überladenen Methode kann die Subskription des Status einer bestimmten Anwendung gesteuert werden. Die Methode 'processApplicationStatus()' wird stets aufgerufen, wenn für eine Anwendung, die den Kriterien entspricht, eine Verbindung zu {{site.data.keyword.iot_short_notm}} hergestellt wird bzw. diese getrennt wird.
## Ereignisse von Geräten publizieren
{: #publishing_events_devices}

Das folgende Codebeispiel zeigt, wie Anwendungen Ereignisse so publizieren können, als stammten diese von einem Gerät.

```

    myClient.connect()

    //Zu publizierendes Ereignis generieren
    JsonObject event = new JsonObject();
    event.addProperty("name", "foo");
    event.addProperty("cpu",  60);
    event.addProperty("mem",  40);

    // Ereignis im Namen eines Geräts publizieren
    myClient.publishEvent(deviceType, deviceId, "blink", event);
```

### Ereignisse mithilfe von HTTP publizieren
{: #publishing_events_http}

Zusätzlich zu MQTT können Sie Ihre Anwendungen so konfigurieren, dass sie Ereignisse in {{site.data.keyword.iot_short_notm}} mithilfe von HTTP publizieren. Führen Sie dazu folgende Schritte aus:

* Erstellen Sie die Anwendungsclientinstanz mithilfe der Eigenschaftendatei.
* Erstellen Sie das Ereignis, das publiziert werden soll.
* Geben Sie den Ereignisnamen, den Gerätetyp und die Geräte-ID an.
* Publizieren Sie das Ereignis mithilfe der Methode `publishEventOverHTTP`(), wie im folgenden Code gezeigt:

```

    	ApplicationClient myClient = new ApplicationClient(props);

    	JsonObject event = new JsonObject();
    	event.addProperty("name", "foo");
    	event.addProperty("cpu",  90);
    	event.addProperty("mem",  70);

    	code = myClient.publishEventOverHTTP(deviceType, deviceId, "blink", event);
```

Das vollständige Codebeispiel finden Sie im folgenden Anwendungsbeispiel:

[HttpApplicationDeviceEventPublish](https://github.com/ibm-messaging/iot-application-samples/blob/master/java/standalone-samples/src/main/java/com/ibm/iotf/sample/client/application/HttpApplicationDeviceEventPublish.java)

Je nach den Einstellungen in der Eigenschaftendatei wird das Ereignis mit der Methode `publishEventOverHTTP()` entweder im Quickstart-Modus oder im Modus des registrierten Ablaufs publiziert. Wenn `quickstart` die in der Eigenschaftendatei angegebene Organisations-ID ist, publiziert die Methode `publishEventOverHTTP()` das Ereignis mithilfe des {{site.data.keyword.iot_short_notm}}-Quickstart-Service im einfachen HTTP-Format. Wenn eine gültige registrierte Organisation in der Eigenschaftendatei angegeben ist, wird das Ereignis stets mithilfe von HTTPS publiziert, damit die gesamte Kommunikation geschützt ist.

Das HTTP-Protokoll bietet die Zustellung 'höchstens einmal', die der Servicequalitätsstufe 'höchstens einmal' (QoS 0) des MQTT-Protokolls ähnelt. Wenn Sie die Zustellungsart 'höchstens einmal' zum Publizieren von Ereignissen verwenden, muss die Anwendung Wiederholungslogik implementieren, falls ein Fehler auftritt. 


## Befehle in Geräten publizieren
{: #publishing_commands_devices}

Anwendungen können Befehle in verbundenen Geräten publizieren, wie im folgenden Beispiel gezeigt: 

```

    myClient.connect()

    //Zu publizierendes Ereignis generieren
    JsonObject data = new JsonObject();
    data.addProperty("name", "stop-rotation");
    data.addProperty("delay",  0);

    //Registrierter Ablauf lässt QoS 0, 1 und 2 zu
    myAppClient.publishCommand(deviceType, deviceId, "stop", data);
```


## Beispiele
{: #examples}


-  [MQTTApplicationDeviceEventPublish](https://github.com/ibm-messaging/iot-application-samples/blob/master/java/standalone-samples/src/main/java/com/ibm/iotf/sample/client/application/MQTTApplicationDeviceEventPublish.java) - Eine Beispielwendung, die die Vorgehensweise beim Publizieren von Geräteereignissen veranschaulicht.
-   [RegisteredApplicationCommandPublish](https://github.com/ibm-messaging/iot-application-samples/blob/master/java/standalone-samples/src/main/java/com/ibm/iotf/sample/client/application/RegisteredApplicationCommandPublish.java) - Eine Beispielanwendung, die die Vorgehensweise beim Publizieren eines Befehls in einem Gerät veranschaulicht.
-  [RegisteredApplicationSubscribeSample](https://github.com/ibm-messaging/iot-application-samples/blob/master/java/standalone-samples/src/main/java/com/ibm/iotf/sample/client/application/RegisteredApplicationSubscribeSample.java) - Eine Beispielanwendung, die die Vorgehensweise beim Subskribieren unterschiedlicher Ereignisse wie beispielsweise Geräteereignisse, Gerätebefehle, Gerätestatus und Anwendungsstatus veranschaulicht.
-   [SharedSubscriptionSample](https://github.com/ibm-messaging/iot-application-samples/blob/master/java/standalone-samples/src/main/java/com/ibm/iotf/sample/client/application/SharedSubscriptionSample.java) - Eine Beispielanwendung, die die Vorgehensweise beim Erstellen einer skalierbaren Anwendung veranschaulicht, die die Nachrichtenlast gleichmäßig auf mehrere Instanzen der Anwendung aufteilt.
-  [Beispiel für Backup und Wiederherstellung](https://github.com/ibm-messaging/iot-backup-restore-sample) - Ein Beispiel, das die Vorgehensweise beim Backup und Wiederherstellen der Gerätekonfiguration in einer Cloudant NoSQL-Datenbank veranschaulicht.
