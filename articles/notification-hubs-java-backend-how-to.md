<properties linkid="notification-hubs-java-back-end-how-to" urlDisplayName="How to use Notification Hubs with Java" pageTitle="How to use Notification Hubs with Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"></tags>

# Использование концентраторов уведомлений из Java/PHP

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ru-ru/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/ru-ru/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

Вы можете обращаться ко всем функциям концентраторов уведомлений из серверной части Java/PHP/Ruby, используя интерфейс REST концентраторов уведомлений в соответствии с описанием в разделе MSDN [Интерфейсы REST концентраторов уведомлений][Интерфейсы REST концентраторов уведомлений].

В этом разделе описывается:

-   построение клиента REST для функций концентраторов уведомлений на языке JAVA;
-   использование [Учебника по началу работы][Учебника по началу работы] с выбранной мобильной платформой, реализация серверной части на языке Java.

## <a name="client-interface"></a>Интерфейс клиента

Основной интерфейс клиента может предоставлять те же методы, которые доступны в [SDK концентраторов уведомлений .NET][SDK концентраторов уведомлений .NET]. Это позволяет напрямую переводить все руководства и примеры, доступные на этом сайте в настоящий момент и пополняемые интернет-сообществом.

Весь доступный код находится в [Примере оболочки Java REST][Примере оболочки Java REST].

Например, чтобы создать клиента, необходимо выполнить следующие действия.

    new NotificationHub("connection string", "hubname");    

Чтобы реализовать регистрацию в iOS (аналогично регистрации для Windows, Android, Windows Phone и Kindle Fire), выполните следующие действия.

    String id = hub.createRegistrationId();
    AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.upsertRegistration(reg);

Отправка собственного уведомления iOS.

    Notification n = Notification.createAppleNotifiation("APNS body");
    hub.sendNotification(n);

## <a name="implementation"></a>Реализация

Если вы еще этого не сделали, выполните шаги [Учебника по началу работы][Учебника по началу работы] до последнего раздела, в котором требуется реализовать серверную часть.
Кроме того, при желании вы можете использовать код из [Примера оболочки Java REST][Примере оболочки Java REST] и перейти напрямую в раздел [Завершение работы с учебником][Завершение работы с учебником].

Подробные сведения о реализации полноценной оболочки REST можно найти в [MSDN][MSDN]. В этом разделе описывается реализация основных действий на языке Java, необходимых для доступа к конечным точкам REST концентраторов уведомлений.

1.  Проанализируйте строку подключения
2.  Создайте маркер проверки подлинности
3.  Выполните вызов HTTP

В фрагментах кода, указанных ниже, используются следующие компоненты:

-   [Apache HttpComponents][Apache HttpComponents]
-   [Apache Commons-Codec][Apache Commons-Codec]
-   [Apache Commons-Io][Apache Commons-Io]

### Проанализируйте строку подключения

Ниже показан основной класс, реализующий клиента, конструктор которого выполняет анализ строки подключения:

    public class NotificationHub {

        private static final String APIVERSION = "?api-version=2013-10";
        private static final String CONTENT_LOCATION_HEADER = "Location";
        private String endpoint;
        private String hubPath;
        private String SasKeyName;
        private String SasKeyValue;

        private HttpClient httpClient;

        public NotificationHub(String connectionString, String hubPath) {
            this.httpClient = HttpClients.createDefault();
            this.hubPath = hubPath;

            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);

            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.endpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.SasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.SasKeyValue = parts[i].substring(16);
                }
            }
        }
    }

### Создание маркера безопасности

Сведения о создании маркера безопасности доступны [здесь][здесь].
Для создания маркера на основе URI текущего запроса и учетных данных, извлеченных из строки подключения, необходимо добавить следующий метод в класс **NotificationHub**.

    private String generateSasToken(URI uri) {
        String targetUri;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Convert raw bytes to Hex
            String signature = URLEncoder.encode(
                    Base64.encodeBase64String(rawHmac), "UTF-8");

            // construct authorization string
            String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + SasKeyName;
            return token;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

### Отправка уведомления

Сначала следует определить класс, представляющий уведомление.

    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;
    import org.apache.http.entity.ContentType;

    public class Notification {
        private Map<String, String> headers = new HashMap<String, String>();
        private String body;
        private ContentType contentType;

        public static Notification createWindowsNotification(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windows");

            if (body.contains("<toast>"))
                n.headers.put("X-WNS-Type", "wns/toast");
            if (body.contains("<tile>"))
                n.headers.put("X-WNS-Type", "wns/tile");
            if (body.contains("<badge>"))
                n.headers.put("X-WNS-Type", "wns/badge");
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createAppleNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "apple");
            return n;
        }

        public static Notification createGcmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "gcm");
            return n;
        }

        public static Notification createAdmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "adm");
            return n;
        }

        public static Notification createMpnsNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windowsphone");

            if (body.contains("<wp:Toast>")) {
                n.headers.put("X-WindowsPhone-Target", "toast");
                n.headers.put("X-NotificationClass", "2");
            }
            if (body.contains("<wp:Tile>")) {
                n.headers.put("X-WindowsPhone-Target", "tile");
                n.headers.put("X-NotificationClass", "1");
            }
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createTemplateNotification(
                Map<String, String> properties) {
            Notification n = new Notification();
            StringBuffer buf = new StringBuffer();
            buf.append("{");
            for (Iterator<String> iterator = properties.keySet().iterator(); iterator
                    .hasNext();) {
                String key = iterator.next();
                buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
                if (iterator.hasNext())
                    buf.append(",");
            }
            buf.append("}");
            n.body = buf.toString();
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "template");
            return n;
        }

        public Map<String, String> getHeaders() { return headers; }

        public void setHeaders(Map<String, String> headers) { this.headers = headers; }

        public String getBody() { return body; }

        public void setBody(String body) { this.body = body; }

        public ContentType getContentType() { return contentType; }

        public void setContentType(ContentType contentType) { this.contentType = contentType; }
    }

Этот класс представляет собой контейнер для собственного тела уведомления, либо набор свойств, в случае с шаблонным уведомлением, а также набор заголовков, содержащих свойства формата (собственная платформа или шаблон) и специальные свойства платформы (например, свойство срока действия Apple и заголовки WNS). Кроме того, происходит определение некоторых вспомогательных конструкторов для создания уведомлений часто используемых типов.

Обратитесь к [Документации по REST API для концентраторов уведомлений][Документации по REST API для концентраторов уведомлений] и изучите форматы специализированных платформ уведомлений, чтобы узнать обо всех доступных параметрах.

Имея этот класс, мы можем создавать методы отправки уведомлений внутри класса **NotificationHub**.

    public void sendNotification(Notification notification) {
        sendNotification(notification, "");
    }

    public void sendNotification(Notification notification, Set<String> tags) {
        if (tags.isEmpty())
            throw new IllegalArgumentException(
                    "tags has to contain at least an element");

        StringBuffer exp = new StringBuffer();
        for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
            exp.append(iterator.next());
            if (iterator.hasNext())
                exp.append(" || ");
        }

        sendNotification(notification, exp.toString());
    }

    public void sendNotification(Notification notification, String tagExpression) {
        HttpPost post = null;
        try {
            URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
            post = new HttpPost(uri);
            post.setHeader("Authorization", generateSasToken(uri));

            if (tagExpression != null && !"".equals(tagExpression)) {
                post.setHeader("ServiceBusNotification-Tags", tagExpression);
            }

            for (String header : notification.getHeaders().keySet()) {
                post.setHeader(header, notification.getHeaders().get(header));
            }

            post.setEntity(new StringEntity(notification.getBody()));
            HttpResponse response = httpClient.execute(post);

            if (response.getStatusLine().getStatusCode() != 201) {
                String msg = "";
                if (response.getEntity() != null
                        && response.getEntity().getContent() != null) {
                    msg = IOUtils.toString(response.getEntity().getContent());
                }
                throw new RuntimeException("Error: " + response.getStatusLine()
                        + " body: " + msg);
            }

        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (post != null)
                post.releaseConnection();
        }
    }

Указанные выше методы отправляют запрос HTTP POST в конечную точку "/messages" концентратора уведомлений, с надлежащим телом и заголовками для отправки уведомления.

## <a name="complete-tutorial"></a>Завершение работы с учебником

Теперь вы можете завершить работу с учебником по началу работы, отправив уведомление из серверной части Java.

Инициализируйте клиент концентратора уведомлений (замените строку подключения и имя концентратора в соответствии с инструкциями в [учебнике по началу работы][Учебника по началу работы]):
 NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

Затем добавьте код отправки, определяемый целевой мобильной платформой.

### Магазин Windows и Windows Phone 8.1 (без Silverlight)

    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);

### iOS

    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);

### Android

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createGcmNotification(message);
    hub.sendNotification(n);

### Windows Phone 8.0 и 8.1 Silverlight

    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);

### Kindle Fire

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);

После выполнения кода Java на целевом устройстве должно отобразиться уведомление.

## <a name="next-steps"></a> Дальнейшие действия

В этом разделе было показано, как создать простой клиент Java REST для концентраторов уведомлений. На данном этапе можно сделать следующее.

-   Загрузить полный [Образец оболочки Java REST][Примере оболочки Java REST], содержащий весь указанный выше код, а также схему управления регистрацией.
-   Продолжить изучение функции тегов в концентраторах уведомлений в [учебнике "Срочные новости"]
-   Изучить отправку уведомлений отдельным пользователям в [учебнике "Уведомление пользователей"]

  [Java]: /ru-ru/documentation/articles/notification-hubs-java-backend-how-to/ "Java"
  [PHP]: /ru-ru/documentation/articles/notification-hubs-php-backend-how-to/ "PHP"
  [Интерфейсы REST концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/dn223264.aspx
  [Учебника по началу работы]: http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-ios-get-started/
  [SDK концентраторов уведомлений .NET]: http://msdn.microsoft.com/ru-ru/library/jj933431.aspx
  [Примере оболочки Java REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
  [Завершение работы с учебником]: #complete-tutorial
  [MSDN]: http://msdn.microsoft.com/ru-ru/library/dn530746.aspx
  [Apache HttpComponents]: http://hc.apache.org/httpcomponents-client-ga/
  [Apache Commons-Codec]: http://commons.apache.org/proper/commons-codec/
  [Apache Commons-Io]: http://commons.apache.org/proper/commons-io/
  [здесь]: http://msdn.microsoft.com/ru-ru/library/dn495627.aspx
  [Документации по REST API для концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/dn495827.aspx
