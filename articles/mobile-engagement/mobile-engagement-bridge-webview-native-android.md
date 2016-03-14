<properties 
	pageTitle="Создание моста между Android WebView и собственным пакетом SDK Android Mobile Engagement" 
	description="Описывает, как создать мост между WebView с Javascript и собственным пакетом SDK Android Mobile Engagement"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="piyushjo" />

#Создание моста между Android WebView и собственным пакетом SDK Android Mobile Engagement

> [AZURE.SELECTOR]
- [Мост Android](mobile-engagement-bridge-webview-native-android.md)
- [Мост iOS](mobile-engagement-bridge-webview-native-ios.md)

Некоторые мобильные приложения представляют собой гибридные приложения. В этом случае само приложение разрабатывается на основе Android, но некоторые или даже все окна отображаются с помощью Android WebView. Тем не менее, пакет SDK Android Mobile Engagement можно использовать в таких приложениях, и в этом учебнике описывается, как это сделать. Следующий пример кода основан на документации по Android, с которой можно ознакомиться [здесь](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Он описывает, как с помощью этого задокументированного подхода можно реализовать то же самое для часто используемых методов пакета SDK Android Mobile Engagement, чтобы Webview из гибридного приложения также могло инициировать запросы на отслеживание событий, заданий, ошибок и информации о приложении во время их передачи через пакет SDK для Android.

1. Во-первых, полностью изучите наш [Учебник "Приступая к работе"](mobile-engagement-android-get-started.md) для интеграции пакета SDK Android Mobile Engagement в свое гибридное приложение. После этого ваш метод `OnCreate` будет выглядеть следующим образом.  
    
		@Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	        engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
	        EngagementAgent.getInstance(this).init(engagementConfiguration);
	    }

2. Теперь убедитесь, что в гибридном приложении есть окно с веб-представлением. Его код будет похож на следующий код, в котором мы загружаем локальный файл HTML **Sample.html** в Webview в методе `onCreate` вашего окна.

	    private void SetWebView() {
	        WebView myWebView = (WebView) findViewById(R.id.webview);
	        myWebView.loadUrl("file:///android_asset/Sample.html");
		}

		protected void onCreate(Bundle savedInstanceState) {
			...
			...
			SetWebView();
	    }

3. Теперь создайте файл моста с именем **WebAppInterface**, который создает оболочку для некоторых часто используемых методов пакета SDK Android Mobile Engagement с помощью подхода `@JavascriptInterface`, описанного в [документации Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

		import android.content.Context;
		import android.os.Bundle;
		import android.util.Log;
		import android.webkit.JavascriptInterface;
		
		import com.microsoft.azure.engagement.EngagementAgent;
		
		import org.json.JSONArray;
		import org.json.JSONObject;
		
		public class WebAppInterface {
		    Context mContext;
		
		    /** Instantiate the interface and set the context */
		    WebAppInterface(Context c) {
		        mContext = c;
		    }
		
		    @JavascriptInterface
		    public void sendEngagementEvent(String name, String extras ){
		        EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void startEngagementJob(String name, String extras ){
		        EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void endEngagementJob(String name){
		        EngagementAgent.getInstance(mContext).endJob(name);
		    }
		
		    @JavascriptInterface
		    public void sendEngagementError(String name, String extras ){
		        EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
		    }
		
		    @JavascriptInterface
		    public void sendEngagementAppInfo(String appInfo){
		        EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
		    }
		
		    public Bundle ParseExtras(String input) {
		        Bundle extras = new Bundle();
		
		        try {
		            JSONObject jObject = new JSONObject(input);
		            extras.putString(jObject.names().getString(0),
		                    jObject.get(jObject.names().getString(0)).toString());
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		        return extras;
		    }
		}  

4. После создания указанного выше файла моста необходимо убедиться, что он связан с нашим веб-представлением. Чтобы это сделать, нужно изменить ваш метод `SetWebview`, так чтобы он выглядел следующим образом:

	    private void SetWebView() {
	        WebView myWebView = (WebView) findViewById(R.id.webview);
	        myWebView.loadUrl("file:///android_asset/Sample.html");
	        WebSettings webSettings = myWebView.getSettings();
	        webSettings.setJavaScriptEnabled(true);
	        myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
	    }

5. В приведенном выше фрагменте кода мы вызвали метод `addJavascriptInterface`, чтобы связать наш класс моста с веб-представлением, а также создали дескриптор **EngagementJs** для вызова методов из файла моста.

6. Теперь создайте следующий файл с именем **Sample.html** в своем проекте в папке с именем **assets**, которая загружается в Webview. В этом файле мы будем вызывать методы из файла моста.

		<!doctype html>
		<html>
		    <head>
		        <style type='text/css'>
		            html { font-family:Helvetica; color:#222; }
		            h1 { color:steelblue; font-size:22px; margin-top:16px; }
		            h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
		        </style>
		
		        <script type="text/javascript">
		
		            window.onerror = function(err)
		            {
		              log('window.onerror: ' + err);
		            }
		
		            send = function(inputId)
		            {
		                var input = document.getElementById(inputId);
		                if(input)
		                {
		                    var value = input.value;
		                    // Example of how extras info can be passed with the Engagement logs
		                    var extras = '{"CustomerId":"MS290011"}';
		
		                    if(value && value.length > 0)
		                    {
		                        switch(inputId)
		                        {
		                            case "event":
		                            EngagementJs.sendEngagementEvent(value, extras);
		                            break;
		
		                            case "job":
		                            EngagementJs.startEngagementJob(value, extras);
		                            window.setTimeout( function()
		                            {
		                              EngagementJs.endEngagementJob(value);
		                            }, 10000 );
		                            break;
		
		                            case "error":
		                            EngagementJs.sendEngagementError(value, extras);
		                            break;
		
		                            case "appInfo":
		                            EngagementJs.sendEngagementAppInfo({"customer_name":value});
		                            break;
		                        }
		                    }
		                }
		            }
		        </script>
		    </head>
		    <body>
		        <h1>Bridge Tester</h1>
		        <div id='engagement'>
		            <h2>Event</h2>
		            <input type="text" id="event" size="35">
		            <button onclick="send('event')">Send</button>
		
		            <h2>Job</h2>
		            <input type="text" id="job" size="35">
		            <button onclick="send('job')">Send</button>
		
		            <h2>Error</h2>
		            <input type="text" id="error" size="35">
		            <button onclick="send('error')">Send</button>
		
		            <h2>AppInfo</h2>
		            <input type="text" id="appInfo" size="35">
		            <button onclick="send('appInfo')">Send</button>
		        </div>
		    </body>
		</html>

8. Обратите внимание на следующие моменты, касающиеся файла HTML выше:

	- 	Он содержит набор полей ввода, в которые можно ввести данные для использования в качестве имен для событий, заданий, ошибок и информации о приложении. При нажатии на кнопку рядом с ним выполняется вызов Javascript, который в конечном итоге вызывает методы из файла моста, чтобы передать этот вызов пакету SDK Android Mobile Engagement. 
	- 	Мы добавляем теги для указания дополнительной статической информации для событий, заданий и даже ошибок, чтобы показать, как это можно сделать. Эти дополнительные сведения отправляются в виде строки JSON, которая, если заглянуть в файл `WebAppInterface`, анализируется, помещается в Android `Bundle` и передается вместе с отправкой событий, заданий и ошибок. 
	- 	Задание Mobile Engagement запускается под именем, указанным в поле ввода, работает в течение 10 секунд и завершается. 
	- 	Информация о приложении Mobile Engagement или тег передаются с "customer\_name" в виде статического ключа и значения, введенных в поле ввода в качестве значения тега. 
 
9. Запустите приложение и вы увидите следующее. Теперь укажите какое-нибудь имя для тестового события (например такое, как указано ниже) и щелкните **Отправить** под ним.

	![][1]

10. Если теперь перейти на вкладку **Мониторинг** своего приложения и раскрыть категорию **События -> Сведения**, вы увидите, что это событие отображается вместе со статическими сведениями о приложении, которые мы отправляем.

	![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png

<!---HONumber=AcomDC_0302_2016-->