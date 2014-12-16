<properties title="Azure Notification Hubs Secure Push" pageTitle="Концентраторы уведомлений Azure, безопасные push-уведомления" metaKeywords="push-уведомления Azure, концентраторы уведомлений Azure, безопасные push-уведомления" description="Learn how to send secure push notifications to an Android app from Azure. Code samples written in Java and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" manager="timlt" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

#Безопасные push-уведомления посредством концентраторов уведомлений Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
		<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android" class="current">Android</a>
</div>

Поддержка push-уведомлений в Microsoft Azure позволяет получить доступ к простой в использовании многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для отдельных пользователей, так и для корпоративных приложений для мобильных платформ. 

Из-за ограничений, связанных с правовыми нормами или обеспечением безопасности, иногда в уведомлении могут присутствовать данные, которые нельзя передать через стандартную инфраструктуру push-уведомлений. В этом учебнике рассказывается о том, как реализовать этот принцип при отправке важной информации через защищенное соединение с проверкой подлинности, установленное между устройством клиента и серверной частью приложения.

На высоком уровне поток можно представить следующим образом.

1. Серверная часть приложения:
	- Сохраняет полезную нагрузку в базе данных серверной части.
	- Отправляет идентификатор этого уведомления устройству (защищаемые сведения не передаются).
2. Приложение на устройстве при получении уведомления:
	- Устройство связывается с серверной частью и запрашивает полезную нагрузку.
	- Приложение может показывать полезную нагрузку в виде уведомления на устройстве.

Важно отметить, что в предыдущем потоке (и в этом учебнике) подразумевается, что устройство сохраняет маркер проверки подлинности в локальном хранилище после входа пользователя. Это гарантирует совершенно беспрепятственную работу, так как устройство может получать полезную нагрузку с использованием этого маркера. Если приложение не сохраняет маркеры проверки подлинности на устройстве, или если истек срок действия маркеров, приложение устройства, после получения уведомления, должно отобразить общее уведомление, предлагая пользователю запустить приложение. Затем приложение выполняет проверку подлинности пользователя и отображает полезную нагрузку уведомления.

В этом учебнике по работе с безопасными push-уведомлениями показано, как безопасно отправлять push-уведомление. Данное руководство является продолжением другого учебника под названием **Уведомление пользователей**, поэтому необходимо сначала выполнить шаги в указанном учебнике.

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили концентратор уведомлений в соответствии с описанием в учебнике [Приступая к работе с концентраторами уведомлений (Android)](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-android-get-started/).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Внесение изменений в проект Android

После того как вы изменили серверную часть приложения для отправки только *идентификатора* уведомления, необходимо внести изменения в приложение Android для обработки этого уведомления и осуществления обратного вызова к серверной части. Вызов нужен для извлечения конфиденциального сообщения, которое следует показать пользователю.
Для этого убедитесь, что приложение Android может пройти проверку подлинности на вашем сервере при получении push-уведомлений.

Теперь мы внесем изменения в процесс *входа*, чтобы сохранить заголовок проверки подлинности в общих настройках приложения. Аналогичные механизмы можно использовать для хранения любых маркеров проверки подлинности (например, маркеры OAuth), которые приложение может использовать без учетных данных пользователя.

1. В проекте приложения Android добавьте следующие константы в начало класса **MainActivity**:

		public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
		public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Также в классе **MainActivity** обновите метод `getAuthorizationHeader()` следующим кодом:

		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
    		EditText password = (EditText) findViewById(R.id.passwordText);
    		String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
    		basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
    	
    		SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
    	
    		return basicAuthHeader;
		}

3. Добавьте операторы import в верхнюю часть файла **MainActivity**:

		import android.content.SharedPreferences;

Теперь мы изменим обработчик, который вызывается при получении уведомления.

4. Измените метод OnReceive в классе **MyHandler** следующим образом:

		public void onReceive(Context context, Bundle bundle) {
	    	ctx = context;   
	    	String secureMessageId = bundle.getString("secureId");
	    	retrieveNotification(secureMessageId);
		}

5. Затем добавьте метод `retrieveNotification()`, заменив заполнитель `{back-end endpoint}` конечной точкой серверной части, полученной при развертывании серверной части:

		private void retrieveNotification(final String secureMessageId) {
			SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
		
			new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
						request.addHeader("Authorization", "Basic "+authorizationHeader);
						HttpResponse response = new DefaultHttpClient().execute(request);
						if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
							Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
							throw new RuntimeException("Error retrieving secure notification");
						}
						String secureNotificationJSON = EntityUtils.toString(response.getEntity());
						JSONObject secureNotification = new JSONObject(secureNotificationJSON);
						sendNotification(secureNotification.getString("Payload"));
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
						return e;
					}
					return null;
				}
			}.execute(null, null, null);
		}
		

Этот метод вызывает серверную часть вашего приложения для извлечения содержимого уведомления с использованием учетных данных, хранящихся в общих настройках. Затем он показывает это содержимое в виде обычного уведомления. Для пользователя приложения такое уведомление ничем не отличается от других push-уведомлений.

Обратите внимание, что случаи отсутствующего или отклоненного свойства заголовков проверки подлинности предпочтительнее обрабатывать на серверном компоненте. Обработка в таких случаях в основном зависит от пользовательского опыта на целевой платформе. Один из таких вариантов - отображение уведомления с обычным предложением проверки подлинности для получения текущего уведомления.

## Запустите приложение

Для запуска приложения выполните следующие действия:

1. Убедитесь, что в Azure выполнено развертывание **AppBackend**. Если используется Visual Studio, запустите приложение веб-API **AppBackend**. Отобразится веб-страница ASP.NET.

2. В Eclipse запустите приложение на физическом устройстве Android или в эмуляторе.

3. В пользовательском интерфейсе приложения Android введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

4. В пользовательском интерфейсе приложения Android нажмите **Вход**. Затем нажмите кнопку **Отправить push-уведомление**.
