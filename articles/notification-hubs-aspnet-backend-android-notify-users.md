<properties title="Azure Notification Hubs Notify Users" pageTitle="Уведомление пользователей посредством центров уведомлений" metaKeywords="Azure push notifications, Azure notification hubs" description="Узнайте, как отправлять безопасные push-уведомления в Azure. Примеры кода написаны на C# с использованием API .NET." documentationCenter="" services="notification-hubs" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="glenga" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

#Уведомление пользователей посредством центров уведомлений

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Универсальное приложение Windows</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть веб-API ASP.NET используется для аутентификации клиентов и создания уведомлений, как показано в разделе руководства [Регистрация из серверной части приложения](http://msdn.microsoft.com/ru-ru/library/dn743807.aspx). Материал этого учебника основан на центре уведомлений, созданном вами в учебнике **Приступая к работе с центрами уведомлений**.

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили центр уведомлений в соответствии с описанием в учебнике [Приступая к работе с центрами уведомлений (Android)](/ru-ru/documentation/articles/notification-hubs-android-get-started/). 
> Если вы используете мобильные службы в качестве серверной службы, см. раздел [Версии мобильных служб](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/) из этого учебника.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Создание проекта Android

Следующий шаг заключается в создании приложения Android.

1. Следуйте указаниям в учебнике [Приступая к работе с центрами уведомлений (Android)],(/ru-ru/documentation/articles/notification-hubs-android-get-started/) чтобы создать приложение и настроить его для получения push-уведомлений из GCM.

2. Откройте файл res/layout/activity_main.xml file и замените содержимое на следующее:
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.notifyusers.MainActivity"
		    android:orientation="vertical">
		    <EditText
		        android:id="@+id/usernameText"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        android:ems="10"
		        android:hint="@string/usernameHint" >
		    </EditText>
		    <EditText
		        android:id="@+id/passwordText"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        android:ems="10"
		        android:hint="@string/passwordHint"
		        android:inputType="textPassword" />
		    <Button
		        android:id="@+id/buttonLogin"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/loginButton"
		        android:onClick="login" />
		    <Button
		        android:id="@+id/buttonSend"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/sendButton"
		        android:onClick="sendPush" />
		</LinearLayout>

2. Откройте файл res/values/strings.xml и добавьте следующие строки кода:

		<string name="usernameHint">Username</string>
	    <string name="passwordHint">Password</string>
	    <string name="loginButton">1. Log in</string>
	    <string name="sendButton">2. Send push</string>

	Основная графическая структура main_activity.xml должна выглядеть следующим образом:

	![][A1]

3. Теперь создайте класс **RegisterClient** в том же пакете, в котором создан класс **MainActivity**. Обязательно замените код "{backend endpoint}" конечной точкой серверной части, полученной в предыдущем разделе.

		import java.io.IOException;
		import java.io.UnsupportedEncodingException;
		import java.util.Set;
		
		import org.apache.http.HttpResponse;
		import org.apache.http.HttpStatus;
		import org.apache.http.client.ClientProtocolException;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.client.methods.HttpPut;
		import org.apache.http.client.methods.HttpUriRequest;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;
		import org.apache.http.util.EntityUtils;
		import org.json.JSONArray;
		import org.json.JSONException;
		import org.json.JSONObject;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.util.Log;
		
		public class RegisterClient {
			private static final String PREFS_NAME = "ANHSettings";
			private static final String REGID_SETTING_NAME = "ANHRegistrationId";
			private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
			SharedPreferences settings;
			protected HttpClient httpClient;
			private String authorizationHeader;
		
			public RegisterClient(Context context) {
				super();
				this.settings = context.getSharedPreferences(PREFS_NAME, 0);
				httpClient =  new DefaultHttpClient();
			}
		
			public String getAuthorizationHeader() {
				return authorizationHeader;
			}
			
			public void setAuthorizationHeader(String authorizationHeader) {
				this.authorizationHeader = authorizationHeader;
			}
			
			public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
				String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

				JSONObject deviceInfo = new JSONObject();
				deviceInfo.put("Platform", "gcm");
				deviceInfo.put("Handle", handle);
				deviceInfo.put("Tags", new JSONArray(tags));

				int statusCode = upsertRegistration(registrationId, deviceInfo);
				
				if (statusCode == HttpStatus.SC_OK) {
					return;
				} else if (statusCode == HttpStatus.SC_GONE){
					settings.edit().remove(REGID_SETTING_NAME).commit();
					registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
					statusCode = upsertRegistration(registrationId, deviceInfo);
					if (statusCode != HttpStatus.SC_OK) {
						Log.e("RegisterClient", "Error upserting registration: " + statusCode);
						throw new RuntimeException("Error upserting registration");
					}
				} else {
					Log.e("RegisterClient", "Error upserting registration: " + statusCode);
					throw new RuntimeException("Error upserting registration");
				}
			}

			private int upsertRegistration(String registrationId, JSONObject deviceInfo)
					throws UnsupportedEncodingException, IOException,
					ClientProtocolException {
				HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
				request.setEntity(new StringEntity(deviceInfo.toString()));
				request.addHeader("Authorization", "Basic "+authorizationHeader);
				request.addHeader("Content-Type", "application/json");
				HttpResponse response = httpClient.execute(request);
				int statusCode = response.getStatusLine().getStatusCode();
				return statusCode;
			}

			private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
				if (settings.contains(REGID_SETTING_NAME))
					return settings.getString(REGID_SETTING_NAME, null);
				
				HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
				request.addHeader("Authorization", "Basic "+authorizationHeader);
				HttpResponse response = httpClient.execute(request);
				if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
					Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
					throw new RuntimeException("Error creating Notification Hubs registrationId");
				}
				String registrationId = EntityUtils.toString(response.getEntity());
				registrationId = registrationId.substring(1, registrationId.length()-1);
				
				settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();
				
				return registrationId;
			}
		}

	Этот компонент реализует вызовы REST, необходимые для связи с серверной частью приложения с целью регистрации в службе push-уведомлений. В данном случае также происходит локальное сохранение идентификаторов registrationId, созданных центром уведомлений в соответствии с описанием в разделе [Регистрация из серверной части приложения](http://msdn.microsoft.com/ru-ru/library/dn743807.aspx). Обратите внимание, что в данном случае используется маркер аутентификации, сохраненный в локальном хранилище при нажатии кнопки **Вход и регистрация**.

4. В классе **MainActivity** удалите частные поля для **NotificationHub** и добавьте поле для **RegisterClient**:

		//private NotificationHub hub;
		private RegisterClient registerClient;
 
5. Затем в методе **onCreate** удалите код инициализации поле **hub** и метод **registerWithNotificationHubs**. Затем добавьте следующие строки, инициализирующие экземпляр класса **RegisterClient**. Метод должен содержать следующие строки:

		@Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        
	        NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	        gcm = GoogleCloudMessaging.getInstance(this);
	        
	        registerClient = new RegisterClient(this);
	        
	        setContentView(R.layout.activity_main);
	    }

6. Затем добавьте следующие методы, обязательно заменив код "{backend endpoint}" конечной точкой серверной части, полученной в предыдущем разделе.

	    @Override
	    protected void onStart() {
	    	super.onStart();
	    	Button sendPush = (Button) findViewById(R.id.buttonSend);
	        sendPush.setEnabled(false);
	    }
	
		public void login(View view) throws UnsupportedEncodingException {
	    	this.registerClient.setAuthorizationHeader(getAuthorizationHeader());
	    	
	    	final Context context = this;
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						String regid = gcm.register(SENDER_ID);
				        registerClient.register(regid, new HashSet<String>());
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to register - " + e.getMessage());
						return e;
					}
					return null;
				}
	
				protected void onPostExecute(Object result) {
					Button sendPush = (Button) findViewById(R.id.buttonSend);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
				}
			}.execute(null, null, null);
	    }
	    
	    public void sendPush(View view) throws ClientProtocolException, IOException {
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
						request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
						HttpResponse response = new DefaultHttpClient().execute(request);
						if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
							Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
							throw new RuntimeException("Error sending notification");
						}
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
						return e;
					}
					return null;
				}
			}.execute(null, null, null);
	    }
	    
		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
	    	EditText password = (EditText) findViewById(R.id.passwordText);
	    	String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
	    	basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
	    	return basicAuthHeader;
		}

	При обратном вызове действия **Вход** происходит создание базового маркера аутентификации на основании введенного имени пользователя и пароля (обратите внимание, что это относится к любому маркеру, используемому в данной схеме аутентификации), затем с помощью "RegisterClient" вызывается серверная часть. При обратном вызове **Send push** происходит вызов серверной части с целью инициировать безопасные уведомления на все устройства пользователя. 

## Выполнение приложения

Для запуска приложения выполните следующие действия:

1. В Eclipse запустите приложение на физическом устройстве Android или в эмуляторе.

2. В пользовательском интерфейсе приложения Android введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

3. В пользовательском интерфейсе приложения Android щелкните **Log in** (Вход). Затем щелкните **Send push** (Отправить push-уведомление).


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG

<!--HONumber=35.1-->
