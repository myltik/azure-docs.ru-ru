<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an Android app from Azure. Code samples written in Java and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Безопасные push-уведомления для концентраторов уведомлений Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
        <a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android" class="current">Android</a>
</div>

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ.

В связи с законодательными ограничениями или из соображений безопасности, приложениям иногда может потребоваться включить в уведомление информацию, которая не может быть передана через стандартную инфраструктуру push-уведомлений. В этом руководстве описываются действия для достижения того же результата, путем отправки конфиденциальной информации через безопасное подключение с проверкой подлинности между клиентским устройством и серверной частью приложения.

На верхнем уровне процесс выглядит так:

1.  Серверная часть приложения:

    -   Хранит конфиденциальную информацию в базе данных серверной части.
    -   Отправляет идентификаторы этой информации на устройство (конфиденциальная информация не пересылается).

2.  Приложение на устройстве при получении уведомления:

    -   Устройство связывается с серверной частью и запрашивает конфиденциальную информацию.
    -   Приложение может показать информацию в виде уведомления на устройстве.

Важно отметить, что в предыдущем процессе (и в учебнике), мы предполагаем, что устройство хранит маркер проверки подлинности в локальном хранилище после того, как пользователь выполнит вход. Это гарантирует полностью удобную работу, так как устройство сможет получить конфиденциальную информацию уведомления с помощью этого маркера. Если приложение не хранит маркеры проверки подлинности на устройстве, либо срок их действия истек, приложение при получении уведомления должно вывести стандартное уведомление с предложением пользователю запустить приложение. Затем приложение производит проверку подлинности пользователя и отображает конфиденциальную информацию.

В этом учебнике по безопасным push-уведомлениям показано, как отправлять push-уведомления безопасно. Учебник основан на учебнике **Уведомление пользователей**, поэтому вам необходимо сначала выполнить этапы указанного учебника.

> [AZURE.NOTE] В данном учебнике предполагается, что вы создали и настроили концентратор уведомлений, как описано в разделе [Приступая к работе с концентраторами уведомлений (Android)][Приступая к работе с концентраторами уведомлений (Android)].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Внесение изменений в проект Android

После того, как вы изменили серверную часть приложения для отправки только *идентификатора* уведомления, необходимо внести изменения в приложение Android для обработки этого уведомления и осуществления обратного вызова к серверной части для извлечения конфиденциального сообщения, которое должно быть отображено.
Для достижения этой цели необходимо убедиться, что приложению Android известно, как осуществлять проверку подлинности на серверной части при получении push-уведомлений.

Мы внесем изменения в процесс *входа* для того, чтобы сохранить заголовок проверки подлинности в общих настройках приложения. Аналогичные механизмы можно использовать для хранения любых маркеров проверки подлинности (например маркеры OAuth), которые потребуются приложению без необходимости ввода данных пользователя.

1.  В проекте приложения Android добавьте следующие константы в начало класса **MainActivity**:

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2.  Также в классе **MainActivity** обновите метод `getAuthorizationHeader()` со следующим кодом:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3.  Добавьте операторы `import` в верхнюю часть файла **MainActivity**:

        import android.content.SharedPreferences;

Теперь мы изменим обработчик, который вызывается при получении уведомления.

1.  В классе **MyHandler** измените метод `OnReceive()` таким образом:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;   
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

2.  Добавьте метод `retrieveNotification()`, подставив вместо `{back-end endpoint}` данные конечной точки серверной части, полученные при ее развертывании:

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

1.  Убедитесь, что **AppBackend** развернут в Azure. При использовании Visual Studio, запустите приложение веб-API **AppBackend**. Отобразится веб-страница ASP.NET.

2.  В Eclipse запустите приложение на физическом устройстве Android или на эмуляторе.

3.  В пользовательском интерфейсе приложения Android введите имя пользователя и пароль. Это могут быть любые совпадающие строки.

4.  В пользовательском интерфейсе приложения Android нажмите **Вход**. Затем нажмите **Отправить push-уведомление**.

