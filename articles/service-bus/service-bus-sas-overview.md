<properties
   pageTitle="Обзор подписанных URL-адресов (SAS) | Microsoft Azure"
   description="Что такое подписи общего доступа, как они работают и как использовать их в кодах Node, PHP и C#."
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="darosa"/>

# Подписи коллективного доступа

*Подписи общего доступа* (SAS) — это основной механизм обеспечения безопасности служебной шины, включая концентраторы событий, обмен сообщениями через посредника (очереди и разделы) и обмен сообщениями с ретрансляцией. В этой статье рассматриваются подписи общего доступа, принципы их работы и порядок использования без привязки к определенной платформе.

## Общие сведения о SAS

Подписи общего доступа представляют собой механизм проверки подлинности на основе безопасных хэшей SHA-256 или URI. Это очень мощный механизм, который используется всеми службами служебной шины. На практике SAS состоит из двух компонентов: *политики общего доступа* и *подписи общего доступа* (которая часто называется *маркером*).

Более подробные сведения об использовании подписанных URL-адресов (SAS) со служебной шиной см. в статье [Проверка подлинности подписанного URL-адреса с помощью служебной шины](service-bus-shared-access-signature-authentication.md).

## Политика общего доступа

SAS всегда начинается с политики. Для каждой политики необходимо выбрать три элемента информации: **имя**, **область** и **разрешения**. **Имя** должно быть уникальным в пределах выбранной области. Область определяется с помощью URI соответствующего ресурса. Для пространства имен служебной шины область — это полное доменное имя (FQDN), такое как **`https://<yournamespace>.servicebus.windows.net/`**.

Доступные для политики разрешения в основном очевидны:

  + Отправка
  + Прослушивание
  + Управление

Вновь созданной политике назначается *первичный ключ* и *вторичный ключ*. Это криптографически строгие ключи. Их нельзя потерять — они всегда будут доступны на портале. Вы можете использовать любой из созданных ключей и создавать их повторно в любое время. Однако в случае повторного создания или изменения первичного ключа в политике любая созданная на его основе подпись общего доступа станет недействительной.

Когда вы создаете пространство имен служебной шины, для него автоматически формируется политика с именем **RootManageSharedAccessKey**, которая содержит все возможные разрешения. Поскольку вы не входите в систему как **привилегированный пользователь**, не используйте эту политику без действительно веской причины. Вы можете создать дополнительные политики для пространства имен на вкладке **Настройка** портала управления Azure. Следует отметить, что к одному уровню дерева в служебной шине (пространству имен, концентратору событий и т. д.) может быть привязано до 12 политик.

## Подпись общего доступа (маркер)

Сама политика не является маркером доступа для служебной шины. Это объект, из которого создается маркер доступа (с помощью первичного или вторичного ключа). Маркер создается путем тщательного составления строки в следующем формате:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Где `signature-string` — это хэш SHA-256 области маркера (в значении **области**, описанном в предыдущем разделе) с добавленным CRLF и временем истечения срока действия (в секундах с начала эпохи: `00:00:00 UTC` 1 января 1970 года).

Хэш напоминает приведенный ниже псевдокод и возвращает 32 байта.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Нехэшируемые значения находятся в строке **SharedAccessSignature**, что позволяет получателю вычислять хэш с теми же параметрами и проверять, возвращается ли при этом такой же результат. URI определяет область, а имя ключа — политику, которую нужно использовать для вычисления хэша. Это требуется для обеспечения безопасности. Если подпись не совпадает с подписью получателя (служебной шины), доступ запрещается. На этом этапе можно убедиться в том, что отправитель получил доступ к ключу и получит указанные в политике права.

## Создание подписи на основе политики

Как создать подпись на основе политики с помощью кода? Рассмотрим несколько примеров.

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Использование подписанного URL-адреса (на уровне HTTP)
 
Теперь, когда вы знаете, как создавать подписи общего доступа для всех сущностей в служебной шине, можно выполнить HTTP-запрос POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
Этот способ работает всегда. SAS можно создать для очереди, раздела, подписки, концентратора событий или ретрансляции. Если для концентраторов событий используется идентификация по издателю, просто добавьте `/publishers/< publisherid>`.

Если маркер SAS назначается отправителю или клиенту, они не могут ни получить ключ напрямую, ни зарезервировать хэш для его получения. Это позволяет контролировать, к чему вы предоставляете доступ и на какое время. Помните, что при изменении первичного ключа в политике любая созданная на ее основе подпись общего доступа станет недействительной.

## Использование подписанного URL-адреса (на уровне AMQP)

В предыдущем разделе вы увидели, как использовать маркер SAS с помощью запроса HTTP POST для отправки данных в служебную шину. Как известно, доступ к служебной шине можно получить с помощью протокола AMQ. Это основной и предпочтительный протокол, используемый для повышения производительности во многих сценариях. Использование маркеров SAS с AMQP описывается в следующем документе [Безопасность AMQP на основе утверждений, версия 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc), который находится в постоянной доработке с 2013 года, но также поддерживается Azure в настоящее время.

Перед началом отправки данных в служебную шину издатель должен отправить маркер SAS внутри сообщения AMQP на четко определенный узел AMQP с именем **$cbs** (вы можете видеть его как «специальную» очередь, используемую службой для получения и проверки всех маркеров SAS). Издатель должен указать поле **ReplyTo** в сообщении AMQP. Это узел, на который служба отправит издателю ответ с результатами проверки маркеров (простая схема «запрос-ответ» между издателем и службой). Этот узел ответа создается «на лету», к слову о «динамическом создании удаленного узла», описанном в спецификации протокола AMQP 1.0. После проверки правильности маркера SAS издатель может продолжить выполнение и отправить данные в службу.

Ниже будет показано, как отправить маркер SAS с помощью протокола AMQP, используя библиотеку [AMQP.Net Lite](http://amqpnetlite.codeplex.com), если нельзя использовать официальный пакет SDK для служебной шины (например, в WinRT, .Net Compact Framework, .Net Micro Framework и Mono) при разработке на C&#35;. Разумеется, эта библиотека полезна для ознакомления с работой защиты на основе утверждений на уровне AMQP, так как вы видели, как работает она на уровне HTTP (с отправлением запроса HTTP POST и маркера SAS в заголовке Authorization). Тем не менее, не волнуйтесь! Если вам не требуются такие глубокие знания о AMQP, можно использовать официальный пакет SDK для служебной шины для приложений .Net Framework, который сделает все за вас, или библиотеку [Azure SB Lite](http://azuresblite.codeplex.com) для всех других платформ (см. выше).

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = "1";
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Указанный выше метод *PutCbsToken()* получает *подключение* (экземпляр класса AMQP Connection, предоставленный библиотекой AMQP .Net Lite), представляющий подключение TCP к службе и параметр *sasToken* (отправляемый маркер SAS). Примечание. Важно, чтобы подключение было создано с применением** внешнего механизма аутентификации SASL** (а не обычного по умолчанию, при котором имя пользователя и пароль используются, когда не нужно отправлять маркер SAS).

Затем издатель создает две ссылки AMQP для отправки маркера SAS и получения ответа (результата проверки маркеров) от службы.

Сообщение AMQP немного сложное, оно содержит массу свойств и больше информации, чем простое сообщение. Маркер SAS помещается в тело сообщения (с помощью конструктора). Свойству **ReplyTo** присваивается имя узла для получения результата проверки по ссылке получателя (вы можете изменить это имя, если хотите, и он будет создан службой динамически). Последние три свойства приложения или пользовательских свойства используются службой, чтобы понять, какие операции ей нужно выполнить. Как описано в черновой спецификации CBS, это должно быть **имя операции** (put-token), **тип маркера**, который размещается, (servicebus.windows.net:sastoken) и, наконец, **«имя» аудитории**, к которой относится маркер (ко всей сущности).

После отправки маркера SAS по ссылке отправителя издателю нужно прочитать ответ по ссылке получателя. Ответ представляет собой простое сообщение AMQP со свойствами приложения **status-code**, которое может содержать те же значения, что и код состояния HTTP.

## Дальнейшие действия

Дополнительные сведения о возможностях использования маркеров SAS см. в [справочнике по API REST служебной шины](https://msdn.microsoft.com/library/azure/hh780717.aspx).

Дополнительные сведения о проверке подлинности с помощью служебной шины см. в статье [Проверка подлинности и авторизация с помощью служебной шины](service-bus-authentication-and-authorization.md).

Больше примеров SAS в сценариях C# и Java Script можно найти в [этой записи блога](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

<!---HONumber=Sept15_HO2-->