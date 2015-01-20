<properties title="Azure Notification Hubs Secure Push" pageTitle="Безопасные push-уведомления посредством концентраторов уведомлений Azure" metaKeywords="push-уведомления Azure, концентраторы уведомлений Azure, безопасные push-уведомления" description="Узнайте, как отправлять безопасные push-уведомления в приложение Android из Azure. Примеры кода написаны на Java и C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" manager="timlt" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

#Безопасные push-уведомления посредством концентраторов уведомлений Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-secure-push/" title="Windows Universal">Универсальная Windows</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
		<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android" class="current">Android</a>
</div>

Поддержка push-уведомлений в Microsoft Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. 

Из-за ограничений, связанных с правовыми нормами или обеспечением безопасности, иногда в уведомлении могут присутствовать данные, которые нельзя передать через стандартную инфраструктуру push-уведомлений. В этом учебнике рассказывается о том, как реализовать этот принцип при отправке важной информации через защищенное соединение с проверкой подлинности, установленное между устройством клиента и серверной частью приложения.

На высоком уровне поток можно представить следующим образом.

1. Серверная часть приложения:
	- Сохраняет полезную нагрузку в базе данных серверной части.
	- Отправляет идентификатор этого уведомления устройству (защищаемые сведения не передаются).
2. Приложение на устройстве при получении уведомления:
	- Устройство связывается с серверной частью и запрашивает полезную нагрузку.
	- Приложение может показывать полезную нагрузку в виде уведомления на устройстве.

Важно отметить, что в предыдущем потоке (и в этом учебнике) подразумевается, что устройство сохраняет маркер проверки подлинности в локальном хранилище после входа пользователя. Это гарантирует совершенно беспрепятственную работу, так как устройство может получать полезную нагрузку с использованием этого маркера. Если приложение не сохраняет маркеры проверки подлинности на устройстве, или если истек срок действия маркеров, приложение устройства, после получения уведомления, должно отобразить общее уведомление, предлагая пользователю запустить приложение. Затем приложение выполняет проверку подлинности пользователя и отображает полезную нагрузку уведомления.

В этом учебнике по безопасности push-уведомлений показано, как безопасно отправлять push-уведомление. Это продолжение другого учебника под названием **Уведомление пользователей**, поэтому сначала следует выполнить шаги в указанном учебнике.

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили концентратор уведомлений в соответствии с описанием в руководстве [Приступая к работе с концентраторами уведомлений (Android)](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-android-get-started/).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Внесение изменений в проект Android

После того как вы изменили серверную часть приложения для отправки только *идентификатора* уведомления, необходимо внести изменения в приложение Android для обработки этого уведомления и осуществления обратного вызова к серверной части, чтобы извлечь конфиденциальное сообщение, которое должно быть отображено.
Для достижения этой цели необходимо убедиться, что приложение Android может пройти проверку подлинности серверной части при получении push-уведомлений.

Мы внесем изменения в процесс *входа*, чтобы сохранить заголовок проверки подлинности в общих настройках приложения. Аналогичные механизмы можно использовать для хранения любых маркеров проверки подлинности (например, маркеров OAuth), которые потребуются приложению без необходимости ввода данных пользователя.

1. В проекте приложения Android добавьте следующие константы в начало класса **MainActivity**:

		public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
		public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Кроме того, в классе **MainActivity** обновите метод getAuthorizationHeader(), добавив следующий код:

		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
    		EditText password = (EditText) findViewById(R.id.passwordText);
    		String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
    		basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
    	
    		SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
    	
    		return basicAuthHeader;
		}

3. В начало файла **MainActivity** добавьте следующие операторы импорта:

		import android.content.SharedPreferences;

Теперь мы изменим обработчик, который вызывается при получении уведомления.

4. В классе **MyHandler** измените метод OnReceive() таким образом:

		public void onReceive(Context context, Bundle bundle) {
	    	ctx = context;   
	    	String secureMessageId = bundle.getString("secureId");
	    	retrieveNotification(secureMessageId);
		}

5. Затем добавьте метод retrieveNotification(), подставив вместо {back-end endpoint} данные конечной точки серверной части, полученные при ее развертывании:

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
		

Этот метод вызывает серверную часть вашего приложения для извлечения содержимого уведомления с использованием учетных данных, хранящихся в общих настройках, и отображения в виде обычного уведомления. Для пользователя приложения уведомление ничем не отличается от других уведомлений.

Обратите внимание, что случаи отсутствующего свойства заголовки проверки подлинности или отклонения предпочтительнее обрабатывать на серверной части. Обработка в таких случаях в основном зависит от пользовательского опыта на целевой платформе. Одним из вариантов является отображение уведомления с обычным предложением пользователю для проверки подлинности, чтобы получить текущее уведомление.

## Запуск приложения

Для запуска приложения выполните следующие действия:

1. Убедитесь, что развертывание **AppBackend** в Azure выполнено. Если используется Visual Studio, запустите приложение веб-API **AppBackend**. Отобразится веб-страница ASP.NET.

2. В Eclipse запустите приложение на физическом устройстве Android или в эмуляторе.

3. В пользовательском интерфейсе приложения Android введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

4. В пользовательском интерфейсе приложения Android нажмите **Вход**. Затем нажмите кнопку **Отправить push-уведомление**.

<!--HONumber=35.2-->
