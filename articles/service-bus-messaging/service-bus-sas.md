---
title: "Аутентификация служебной шины Azure с использованием подписанных URL-адресов | Документация Майкрософт"
description: "Обзор аутентификации служебной шины с помощью подписанных URL-адресов, а также сведения об аутентификации SAS с помощью служебной шины Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: cdbac0fd18ad440ece35881cbe165c3c7eff8914
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Аутентификация служебной шины с помощью подписанных URL-адресов

*Подписанные URL-адреса* (SAS) являются основным механизмом защиты обмена сообщениями служебной шины. В этой статье рассматриваются подписанные URL-адреса, принципы их работы и порядок использования без привязки к определенной платформе.

Аутентификация SAS позволяет проверять подлинность приложений в служебной шине с помощью ключа доступа, настроенного в пространстве имен или в сущности обмена сообщениями (очереди или разделе) с определенными правами. Этот ключ можно использовать для создания маркера SAS, который клиенты могут использовать при проверке подлинности в служебной шине.

Поддержка проверки подлинности SAS включена в пакет Azure SDK версии 2.0 и выше.

## <a name="overview-of-sas"></a>Общие сведения о SAS

Подписи общего доступа представляют собой механизм проверки подлинности на основе безопасных хэшей SHA-256 или URI. Это очень мощный механизм, который используется всеми службами служебной шины. На практике SAS состоит из двух компонентов: *политики общего доступа* и *подписанного URL-адреса* (который часто называется *маркером*).

Проверка подлинности SAS в служебной шине предусматривает настройку соответствующих прав для криптографического ключа в ресурсе служебной шины. Клиенты запрашивают доступ к ресурсам служебной шины, предоставляя маркер SAS. В маркере указывается URI ресурса, к которому требуется доступ, и срок действия. Маркер подписывается настроенным ключом.

Правила авторизации подписанного URL-адреса можно настроить в [ретрансляторах](service-bus-fundamentals-hybrid-solutions.md#relays), [очередях](service-bus-fundamentals-hybrid-solutions.md#queues) и [разделах](service-bus-fundamentals-hybrid-solutions.md#topics) служебной шины.

При проверке подлинности SAS используются следующие элементы.

* [Правило авторизации общего доступа](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): 256-битный основной криптографический ключ в кодировке Base64, необязательный дополнительный ключ, имя ключа и соответствующие права (набор прав на *прослушивание*, *отправку* и *управление*).
* Маркер [подписанного URL-адреса](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider): создается с помощью кода HMAC-SHA256 строки ресурса и криптографического ключа. В строке ресурса указывается URI ресурса, к которому запрашивается доступ, и срок действия. Подпись и другие элементы, описанные в следующих разделах, преобразуются в строку, образуя маркер SAS.

## <a name="shared-access-policy"></a>Политика общего доступа

Важно помнить, что SAS всегда начинается с политики. Для каждой политики необходимо выбрать три элемента информации: **имя**, **область** и **разрешения**. **Имя** должно быть уникальным в пределах выбранной области. Область определяется с помощью URI соответствующего ресурса. Для пространства имен служебной шины область — это полное доменное имя (FQDN), такое как `https://<yournamespace>.servicebus.windows.net/`.

Доступные для политики разрешения в основном очевидны:

* Отправка
* Прослушивание
* управление

Вновь созданной политике назначается *первичный* и *вторичный ключ*. Это криптографически строгие ключи. Их нельзя потерять — они всегда будут доступны на [портал Azure][Azure portal]. Вы можете использовать любой из созданных ключей и создавать их повторно в любое время. Однако в случае повторного создания или изменения первичного ключа в политике любая созданная на его основе подпись общего доступа станет недействительной.

Когда вы создаете пространство имен служебной шины, для него автоматически формируется политика с именем **RootManageSharedAccessKey**, которая содержит все возможные разрешения. Так как вы не входите в систему как **привилегированный пользователь**, не используйте эту политику без действительно веской причины. Вы можете создать дополнительные политики для пространства имен на вкладке **Настройка** портала. Следует отметить, что к одному уровню дерева в служебной шине (пространству имен, очереди и т. д.) может быть привязано до 12 политик.

## <a name="configuration-for-shared-access-signature-authentication"></a>Настройка проверки подлинности подписанного URL-адреса
Правило [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) можно настроить в пространствах имен, очередях или разделах служебной шины. Настройка правила [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) в подписке на служебную шину сейчас не поддерживается, но для безопасного доступа к подпискам можно использовать правила, настроенные в пространстве имен или разделе. Рабочий пример, в котором показана эта процедура, см. в примере [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

В пространстве имен, очереди или разделе служебной шины можно настроить до 12 таких правил. Правила, настроенные для пространства имен служебной шины, применяются ко всем сущностям в этом пространстве.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

На этом рисунке правила авторизации *manageRuleNS*, *sendRuleNS* и *listenRuleNS* применяются к очереди Q1 и разделу T1, правила *listenRuleQ* и *sendRuleQ* — только к очереди Q1 и правило *sendRuleT* — только к разделу T1.

Основные параметры правила [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) .

| Параметр | ОПИСАНИЕ |
| --- | --- |
| *KeyName* |Строка с описанием правила авторизации. |
| *PrimaryKey* |256-битный первичный ключ в кодировке Base64 для подписи и проверки маркера SAS. |
| *SecondaryKey* |256-битный вторичный ключ в кодировке Base64 для подписи и проверки маркера SAS. |
| *AccessRights* |Список прав доступа, предоставляемых правилом авторизации. Эти права могут быть любым набором прав на прослушивание, отправку и управление. |

Если пространство имен служебной шины подготовлено, правило [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) со значением **RootManageSharedAccessKey**, установленным для параметра [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName), создается по умолчанию.

## <a name="generate-a-shared-access-signature-token"></a>Создание подписанного URL-адреса (маркера)

Сама политика не является маркером доступа для служебной шины. Это объект, из которого создается маркер доступа (с помощью первичного или вторичного ключа). Любой клиент, у которого есть доступ к ключам подписи, указанным в правиле авторизации общего доступа, может создать маркер SAS. Маркер создается путем тщательного составления строки в следующем формате:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Где `signature-string` — это хэш SHA-256 области маркера (в значении **области**, описанном в предыдущем разделе) с добавленным CRLF и временем истечения срока действия (в секундах с начала эпохи: `00:00:00 UTC` 1 января 1970 года). 

> [!NOTE]
> Чтобы срок действия маркера не оказался слишком коротким, рекомендуется кодировать значение времени срока действия как минимум как 32-разрядное целое число без знака, а еще лучше — как длинное целое число (64-разрядное).  
> 
> 

Хэш напоминает приведенный ниже псевдокод и возвращает 32 байта.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Нехэшируемые значения находятся в строке **SharedAccessSignature** , что позволяет получателю вычислять хэш с теми же параметрами и проверять, возвращается ли при этом такой же результат. URI определяет область, а имя ключа — политику, которую нужно использовать для вычисления хэша. Это требуется для обеспечения безопасности. Если подпись не совпадает с подписью получателя (служебной шины), доступ запрещается. На этом этапе можно убедиться в том, что отправитель получил доступ к ключу и указанные в политике права.

Обратите внимание, что для этой операции вам следует использовать зашифрованный URI ресурса. URI ресурса — это полный URI ресурса служебной шины, к которому запрашивается доступ. Например, `http://<namespace>.servicebus.windows.net/<entityPath>` или `sb://<namespace>.servicebus.windows.net/<entityPath>`. Вся строка будет выглядеть так: `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

Правило авторизации общего доступа, используемое для подписи, необходимо настроить для сущности, указанной данным URI или одной из ее иерархических родительских сущностей. Например, в предыдущем примере это — `http://contoso.servicebus.windows.net/contosoTopics/T1` или `http://contoso.servicebus.windows.net`.

Маркер SAS действителен для всех ресурсов по универсальному коду `<resourceURI>`, используемому в `signature-string`.

Параметр [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) в маркере SAS ссылается на **keyName** правила авторизации общего доступа, используемого для создания маркера.

Параметр *URL-encoded-resourceURI* должен совпадать с URI, который используется в строке для подписи во время вычисления подписи. Значение должно быть [закодировано с помощью знака процента](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Рекомендуется периодически повторно создавать ключи, используемые в объекте [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Чтобы создать маркер SAS, в приложениях рекомендуется использовать параметр [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey). При повторном создании ключей следует заменить параметр [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) старым первичным ключом. Затем нужно создать первичный ключ заново. Это позволит и дальше использовать для авторизации маркеры, содержащие как старый первичный ключ, так и тот, срок действия которого еще не истек.

В случае взлома, когда отзываются оба ключа, можно повторно создать ключи [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) и [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) в правиле [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), заменив ими старые. В результате этой процедуры все маркеры, подписанные старыми ключами, станут недействительными.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Использование проверки подлинности подписанного URL-адреса в служебной шине

Следующие сценарии включают настройку правил авторизации, создание маркеров SAS и авторизацию клиента.

Полностью рабочий пример приложения служебной шины, демонстрирующий настройку и использование авторизации SAS, см. в статье [Проверка подлинности подписанного URL-адреса с помощью служебной шины](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Сопутствующий пример, демонстрирующий использование правил авторизации SAS, настроенных в пространствах имен или разделах, для защиты подписок служебной шины, доступен здесь: [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Доступ к правилам авторизации общего доступа в пространстве имен

Операции с корнем пространства имен служебной шины требуют проверки подлинности сертификата. Необходимо передать сертификат управления для вашей подписки Azure. Чтобы передать сертификат управления, выполните [эти](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) инструкции на [портале Azure][Azure portal]. Дополнительные сведения о сертификатах управления Azure см. в [обзоре сертификатов Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Конечная точка для доступа к правилам авторизации общего доступа в пространстве имен служебной шины выглядит следующим образом:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Чтобы создать объект [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) в пространстве имен служебной шины, выполните операцию POST в конечной точке с сериализацией информации о правиле в формат JSON или XML. Например: 

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Аналогичным образом, чтобы прочесть правила авторизации, настроенные в пространстве имен, выполните операцию GET в конечной точке.

Чтобы обновить или удалить определенное правило авторизации, используйте следующую конечную точку:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Доступ к правилам авторизации общего доступа в сущности

Доступ к объекту [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), настроенному для очереди или раздела служебной шины, можно получить через коллекцию [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) в соответствующих объектах [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) и [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

В следующем коде показано, как добавлять правила авторизации для очереди.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Использование авторизации подписанного URL-адреса

Приложения, использующие пакет Azure SDK для .NET с библиотеками служебной шины для .NET, могут использовать авторизацию SAS с помощью класса [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . В следующем коде показано, как использовать поставщик маркеров, чтобы отправить сообщения в очередь служебной шины.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Приложения также могут использовать SAS для проверки подлинности с помощью строки подключения SAS в методах, которые принимают строки подключения в качестве параметров.

Обратите внимание, что для использования авторизации SAS с ретрансляторами служебной шины вы можете использовать ключи SAS, настроенные в пространстве имен служебной шины. Если вы явным образом создаете ретранслятор в объекте пространства имен ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) с [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), вы можете задать правила SAS для этого ретранслятора. Чтобы выполнить авторизацию SAS с помощью подписок служебной шины, вы можете использовать ключи SAS, настроенные в пространстве имен или разделе служебной шины.

## <a name="use-the-shared-access-signature-at-http-level"></a>Использование подписанного URL-адреса (на уровне HTTP)

Теперь, когда вы знаете, как создавать подписи общего доступа для всех сущностей в служебной шине, можно выполнить HTTP-запрос POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Этот способ работает всегда. SAS можно создать для очереди, раздела или подписки. 

Если маркер SAS назначается отправителю или клиенту, они не могут ни получить ключ напрямую, ни зарезервировать хэш для его получения. Это позволяет контролировать, к чему вы предоставляете доступ и на какое время. Помните, что при изменении первичного ключа в политике любая созданная на ее основе подпись общего доступа станет недействительной.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Использование подписанного URL-адреса (на уровне AMQP)

В предыдущем разделе вы увидели, как использовать маркер SAS с помощью запроса HTTP POST для отправки данных в служебную шину. Как известно, доступ к служебной шине можно получить с помощью расширенного протокола управления очередью сообщений (AMQP). Этот протокол является предпочтительным, так как обладает хорошей производительностью во многих сценариях. Использование маркеров SAS с AMQP описывается в документе [Безопасность AMQP на основе утверждений, версия 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc). Этот документ находится в постоянной доработке с 2013 года, но сейчас хорошо поддерживается Azure.

Перед началом отправки данных служебной шине издатель должен отправить маркер SAS внутри сообщения AMQP на узел AMQP с именем **$cbs** (этот узел отображается как "специальная" очередь, используемая службой для получения и проверки всех маркеров SAS). Издатель должен указать поле **ReplyTo** в сообщении AMQP. Это узел, на который служба отправит издателю ответ с результатами проверки маркера (простая схема "запрос — ответ" между издателем и службой). Этот узел ответа создается "на лету", в соответствии с "динамическим созданием удаленного узла", описанным в спецификации протокола AMQP 1.0. После проверки правильности маркера SAS издатель может продолжить выполнение и отправить данные в службу.

Ниже показано, как отправить маркер SAS с помощью протокола AMQP и библиотеки [AMQP .NET Lite](https://github.com/Azure/amqpnetlite). Это удобно, если не удается использовать официальный пакет SDK для служебной шины (например, в WinRT, .NET Compact Framework, .NET Micro Framework и Mono), который разрабатывается на языке C\#. Разумеется, эта библиотека полезна для ознакомления с тем, как работает защита на основе утверждений на уровне AMQP. Вы уже видели, как защита работает на уровне HTTP (с отправкой запроса HTTP POST и маркера SAS в заголовке Authorization). Если вам не требуются такие глубокие знания о протоколе AMQP, можно использовать официальный пакет SDK служебной шины для приложений .NET Framework, чтобы выполнить все необходимые действия.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
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
    request.Properties.MessageId = Guid.NewGuid().ToString();
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

Метод `PutCbsToken()` получает *подключение* (экземпляр класса AMQP Connection, предоставляемый библиотекой [AMQP .NET Lite](https://github.com/Azure/amqpnetlite)). Это подключение представляет подключение TCP к службе и параметр *sasToken* (отправляемый маркер SAS). 

> [!NOTE]
> Важно, чтобы подключение было создано с применением **механизма аутентификации SASL EXTERNAL** (а не механизма по умолчанию PLAIN, при котором имя пользователя и пароль используются, когда не нужно отправлять маркер SAS).
> 
> 

Затем издатель создает две ссылки AMQP для отправки маркера SAS и получения ответа (результата проверки маркера) от службы.

Сообщение AMQP содержит набор свойств и больше информации по сравнению с простым сообщением. Маркер SAS составляет тело сообщения (с помощью конструктора). Свойству **ReplyTo** присваивается имя узла для получения результата проверки по ссылке получателя (при желании это имя можно изменить, и оно будет создано службой динамически). Последние три свойства приложения или пользовательских свойства используются службой, чтобы понять, какую операцию ей нужно выполнить. Как описано в черновой спецификации CBS, это должно быть **имя операции** (put-token), **тип маркера** (в этом случае servicebus.windows.net:sastoken) и, наконец, **"имя" аудитории**, к которой относится маркер (вся сущность).

После отправки маркера SAS по ссылке отправителя издателю нужно прочитать ответ по ссылке получателя. Ответ представляет собой простое сообщение AMQP со свойством приложения **status-code** , которое может содержать те же значения, что и код состояния HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Права, необходимые для операций служебной шины

В следующей таблице показаны права доступа, необходимые для выполнения различных операций с ресурсами служебной шины.

| Операция | Требуемое утверждение | Область утверждения |
| --- | --- | --- |
| **Пространство имен** | | |
| Настройка правила авторизации для пространства имен |управление |Любой адрес пространства имен |
| **Регистр служб** | | |
| Перечисление частных политик |управление |Любой адрес пространства имен |
| Начало прослушивания в пространстве имен |Прослушивание |Любой адрес пространства имен |
| Отправка сообщений в прослушиватель в пространстве имен |Отправка |Любой адрес пространства имен |
| **Очередь** | | |
| Создание очереди |управление |Любой адрес пространства имен |
| Удаление очереди |управление |Любой допустимый адрес очереди |
| Перечисление очередей |управление |/$Resources/Queues |
| Получение описания очереди |управление |Любой допустимый адрес очереди |
| Настройка правила авторизации для очереди |управление |Любой допустимый адрес очереди |
| Отправка в очередь |Отправка |Любой допустимый адрес очереди |
| Получение сообщений из очереди |Прослушивание |Любой допустимый адрес очереди |
| Прерывание или завершение обмена сообщениями после получения сообщения в режиме чтения с блокировкой |Прослушивание |Любой допустимый адрес очереди |
| Откладывание сообщения для последующего получения |Прослушивание |Любой допустимый адрес очереди |
| Назначение сообщению статуса недоставленного |Прослушивание |Любой допустимый адрес очереди |
| Получение состояния, связанного с сеансом очереди сообщений |Прослушивание |Любой допустимый адрес очереди |
| Назначение состояния, связанного с сеансом очереди сообщений |Прослушивание |Любой допустимый адрес очереди |
| **Раздел** | | |
| Создание раздела |управление |Любой адрес пространства имен |
| Удаление раздела |управление |Любой допустимый адрес раздела |
| Перечисление разделов |управление |/$Resources/Topics |
| Получение описания раздела |управление |Любой допустимый адрес раздела |
| Настройка правила авторизации для раздела |управление |Любой допустимый адрес раздела |
| Отправка в раздел |Отправка |Любой допустимый адрес раздела |
| **Подписка** | | |
| Создание подписки |управление |Любой адрес пространства имен |
| Удаление подписки |управление |../myTopic/Subscriptions/mySubscription |
| Перечисление подписок |управление |../myTopic/Subscriptions |
| Получение описания подписки |управление |../myTopic/Subscriptions/mySubscription |
| Прерывание или завершение обмена сообщениями после получения сообщения в режиме чтения с блокировкой |Прослушивание |../myTopic/Subscriptions/mySubscription |
| Откладывание сообщения для последующего получения |Прослушивание |../myTopic/Subscriptions/mySubscription |
| Назначение сообщению статуса недоставленного |Прослушивание |../myTopic/Subscriptions/mySubscription |
| Получение состояния, связанного с сеансом раздела |Прослушивание |../myTopic/Subscriptions/mySubscription |
| Назначение состояния, связанного с сеансом раздела |Прослушивание |../myTopic/Subscriptions/mySubscription |
| **Правила** | | |
| Создание правила |управление |../myTopic/Subscriptions/mySubscription |
| Удаление правила |управление |../myTopic/Subscriptions/mySubscription |
| Перечисление правил |Управление или прослушивание |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительную информацию об обмене сообщениями через служебную шину см. в следующих разделах.

* [Базовая информация о Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com