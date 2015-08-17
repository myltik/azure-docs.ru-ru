<properties 
	pageTitle="Уведомление пользователей посредством центров уведомлений" 
	description="Узнайте, как отправлять push-уведомления пользователям в Azure. Примеры кода написаны на Java для Android." 
	documentationCenter="android" 
	services="notification-hubs" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/31/2015" 
	ms.author="wesmc"/>

#Уведомление пользователей посредством центров уведомлений


[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##Обзор

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть ASP.NET WebAPI используется для проверки подлинности клиентов и создания уведомлений, как показано в разделе руководства [Регистрация из серверной части приложения](http://msdn.microsoft.com/library/dn743807.aspx). Материал этого учебника основан на центре уведомлений, созданном вами при изучении учебника [Приступая к работе с центрами уведомлений (Android)](notification-hubs-android-get-started.md).

> [AZURE.NOTE]В этом учебнике подразумевается, что вы создали и настроили центр уведомлений в соответствии с описанием в учебнике [Приступая к работе с центрами уведомлений (Android)](notification-hubs-android-get-started.md). Если вы используете мобильные службы в качестве серверной службы, см. раздел [Версии мобильных служб](../mobile-services-javascript-backend-android-push-notifications-app-users.md) этого учебника.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Создание проекта Android

Следующий шаг заключается в создании приложения Android.

1. Следуйте инструкциям учебника [Приступая к работе с концентраторами уведомлений (Android)](notification-hubs-android-get-started.md), чтобы создать приложение и настроить его на получение push-уведомлений для GCM.

2. Откройте файл **res/layout/activity\_main.xml** и замените его содержимое следующими определениями содержимого:
 
    Это добавляет новые элементы управления EditText для входа в систему как пользователь. Также добавляется поле для тега имени пользователя, который будет добавляться в отправляемые уведомления:
			
		<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
            android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
        
        <EditText
            android:id="@+id/usernameText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/usernameHint"
            android:layout_above="@+id/passwordText"
            android:layout_alignParentEnd="true" />
        <EditText
            android:id="@+id/passwordText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/passwordHint"
            android:inputType="textPassword"
            android:layout_above="@+id/buttonLogin"
            android:layout_alignParentEnd="true" />
        <Button
            android:id="@+id/buttonLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/loginButton"
            android:onClick="login"
            android:layout_above="@+id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="24dp" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="WNS on"
            android:textOff="WNS off"
            android:id="@+id/toggleButtonWNS"
            android:layout_toLeftOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="GCM on"
            android:textOff="GCM off"
            android:id="@+id/toggleButtonGCM"
            android:checked="true"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="APNS on"
            android:textOff="APNS off"
            android:id="@+id/toggleButtonAPNS"
            android:layout_toRightOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessageTag"
            android:layout_below="@id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_tag_hint" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessage"
            android:layout_below="@+id/editTextNotificationMessageTag"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_hint" />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/send_button"
            android:id="@+id/sendbutton"
            android:onClick="sendNotificationButtonOnClick"
            android:layout_below="@+id/editTextNotificationMessage"
            android:layout_centerHorizontal="true" />
        </RelativeLayout>



3. Откройте файл **res/values/strings.xml** и замените определение `send_button` следующими строками, переопределяющими строку для `send_button`, и добавьте строки для других элементов управления:

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="send_button">2. Send Notification</string>
        <string name="notification_message_tag_hint">
			Recipient username tag
		</string>

	Основная графическая структура main\_activity.xml должна выглядеть следующим образом:

	![][A1]

4. Создайте новый класс **RegisterClient** в том же пакете, в котором создан класс `MainActivity`. Используйте приведенный ниже код для файла нового класса.
 
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
            private String Backend_Endpoint;
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;
        
            public RegisterClient(Context context, String backendEnpoint) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
                Backend_Endpoint = backendEnpoint + "/api/register";
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
                HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
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
        
                HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
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

	Этот компонент реализует вызовы REST, необходимые для связи с серверной частью приложения с целью регистрации в службе push-уведомлений. В данном случае также происходит локальное сохранение идентификаторов *registrationId*, созданных концентратором уведомлений в соответствии с описанием в разделе [Регистрация из серверной части приложения](http://msdn.microsoft.com/library/dn743807.aspx). Обратите внимание, что в данном случае используется токен авторизации, сохраненный в локальном хранилище при нажатии кнопки **Log in** (Войти).

5. В классе `MainActivity` удалите или преобразуйте в комментарий частное поле для `NotificationHub`, добавьте поле для класса `RegisterClient` и строку для конечной точки серверной части ASP.NET. Обязательно замените `<Enter Your Backend Endpoint>` фактической конечной точкой серверной части, полученной ранее. Например, `http://mybackend.azurewebsites.net`.


		//private NotificationHub hub;
		private RegisterClient registerClient;
	    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";

 
6. В классе `MainActivity` в методе `onCreate` удалите или преобразуйте в комментарий инициализацию поля `hub` и вызов метода `registerWithNotificationHubs`. Добавьте код для инициализации нового экземпляра класса `RegisterClient`. Метод должен содержать следующие строки:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
    
            MyHandler.mainActivity = this;
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);
    
            //hub = new NotificationHub(HubName, HubListenConnectionString, this);
            //registerWithNotificationHubs();
    
	        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

            setContentView(R.layout.activity_main);
        }

7. В классе `MainActivity` удалите или преобразуйте в комментарий весь метод `registerWithNotificationHubs`. Он не будет использоваться в этом учебнике.

8. В файл **MainActivity.java** добавьте следующие операторы `import`.

		import android.widget.Button;
		import java.io.UnsupportedEncodingException;
		import android.content.Context;
		import java.util.HashSet;
		import android.widget.Toast;
		import org.apache.http.client.ClientProtocolException;
		import java.io.IOException;
		import org.apache.http.HttpStatus;


9. Затем добавьте следующие методы для обработки события нажатия кнопки **Log in** (Войти) и отправки push-уведомлений.

	    @Override
	    protected void onStart() {
	    	super.onStart();
        	Button sendPush = (Button) findViewById(R.id.sendbutton);
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
	                    DialogNotify("MainActivity - Failed to register", e.getMessage());
						return e;
					}
					return null;
				}
	
				protected void onPostExecute(Object result) {
                	Button sendPush = (Button) findViewById(R.id.sendbutton);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
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

        /**
         * This method calls the ASP.NET WebAPI backend to send the notification message
         * to the platform notification service based on the pns parameter.
         *
         * @param pns     The platform notification service to send the notification message to. Must
         *                be one of the following ("wns", "gcm", "apns").
         * @param userTag The tag for the user who will receive the notification message. This string
         *                must not contain spaces or special characters.
         * @param message The notification message string. This string must include the double quotes
         *                to be used as JSON content.
         */
        public void sendPush(final String pns, final String userTag, final String message)
                throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
    
                        String uri = BACKEND_ENDPOINT + "/api/notifications";
                        uri += "?pns=" + pns;
                        uri += "&to_tag=" + userTag;
    
                        HttpPost request = new HttpPost(uri);
                        request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                        request.setEntity(new StringEntity(message));
                        request.addHeader("Content-Type", "application/json");
    
                        HttpResponse response = new DefaultHttpClient().execute(request);
    
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            DialogNotify("MainActivity - Error sending " + pns + " notification", 
								response.getStatusLine().toString());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                        return e;
                    }
    
                    return null;
                }
            }.execute(null, null, null);
        }


	Обработчик `login` кнопки **Log in** (Войти) создает токен обычной проверки подлинности на основании введенного имени пользователя и пароля (обратите внимание, что это относится к любому токену, используемому в данной схеме аутентификации), затем с помощью `RegisterClient` вызывается серверная часть для регистрации.

	Метод `sendPush` вызывает серверную часть, чтобы инициировать безопасное уведомление пользователя по тегу пользователя. Служба уведомлений платформы, на которую нацеливается `sendPush`, зависит от переданной строки `pns`.

10. В классе `MainActivity` измените метод `sendNotificationButtonOnClick` для вызова метода `sendPush`, указав выбранные пользователем службы уведомлений платформы следующим образом.

        /**
         * Send Notification button click handler. This method sends the push notification
         * message to each platform selected.
         *
         * @param v The view
         */
        public void sendNotificationButtonOnClick(View v)
                throws ClientProtocolException, IOException {
    
            String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                    .getText().toString();
            String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                    .getText().toString();
    
            // JSON String
            nhMessage = """ + nhMessage + """;
    
            if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
            {
                sendPush("wns", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
            {
                sendPush("gcm", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
            {
                sendPush("apns", nhMessageTag, nhMessage);
            }
        }



## Запуск приложения


1. Запустите приложение на устройстве или в эмуляторе с помощью Android Studio.

2. В приложении Android введите имя пользователя и пароль. Они должны быть одинаковыми строковыми значениями без пробелов или специальных знаков.

3. В приложении Android нажмите кнопку **Log in** (Войти). Дождитесь появления всплывающего уведомления **Logged in and registered** (Вход и регистрация выполнены). Это активирует кнопку **Send Notification** (Отправить уведомление).

	![][A2]

4. С помощью переключателей включите все платформы, на которых вы запускали приложение и регистрировали пользователя.
5. Введите имя пользователя, который получит уведомление. Он должен быть зарегистрирован для получения уведомлений на целевых устройствах.

6. Введите сообщение для пользователя, получающего push-уведомление.
7. Нажмите кнопку **Send Notification** (Отправить уведомление). Каждое устройство с регистрацией, соответствующей тегу имени пользователя, получит push-уведомление.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png

 

<!---HONumber=August15_HO6-->