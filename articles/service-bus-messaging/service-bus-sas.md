---
title: Управление доступом к служебной шине Azure с помощью подписанных URL-адресов | Документация Майкрософт
description: Обзор управления доступом к служебной шине с помощью подписанных URL-адресов, а также сведения об авторизации SAS в служебной шине Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm
ms.openlocfilehash: 420f4573fbe8b5139a4e1e5fa4dea3404c4e099d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312531"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Управление доступом к служебной шине с помощью подписанных URL-адресов

*Подписанные URL-адреса* (SAS) являются основным механизмом защиты обмена сообщениями служебной шины. В этой статье рассматриваются подписанные URL-адреса, принципы их работы и порядок использования без привязки к определенной платформе.

Подписанный URL-адрес защищает доступ к служебной шине на основе правил авторизации. Эти правила настраиваются в пространстве имен или в сущности обмена сообщениями (ретрансляторе, очереди или разделе). Право авторизации имеет имя, связано с определенными правами и содержит пару криптографических ключей. Вы можете использовать имя и ключ правила в пакете SDK служебной шины и в собственном коде, чтобы создать маркер SAS. Затем клиент может передать маркер в служебную шину для подтверждения авторизации запрашиваемой операции.

## <a name="overview-of-sas"></a>Общие сведения о SAS

Подписанные URL-адреса — это механизм авторизации на основе утверждений, использующий простые маркеры. При использовании SAS ключи никогда не передаются по сети. Ключи криптографически подписывают сведения, которые впоследствии будут проверены службой. SAS можно использовать так же, как и имя пользователя и пароль, где клиент немедленно получает имя и соответствующий ключ правила авторизации. Кроме того, SAS можно использовать подобно федеративной модели безопасности, где клиент получает подписанный маркер доступа с ограниченным временем действия из службы маркеров безопасности, не используя ключ для подписи.

Проверка подлинности SAS в служебной шине выполняется с помощью именованных [правил авторизации общего доступа](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) со связанными правами доступа и пары первичного и вторичного криптографических ключей. Ключи представляют собой 256-разрядные значения в кодировке Base64. Вы можете настроить правила на уровне пространства имен в [ретрансляторах](service-bus-fundamentals-hybrid-solutions.md#relays), [очередях](service-bus-fundamentals-hybrid-solutions.md#queues) и [разделах](service-bus-fundamentals-hybrid-solutions.md#topics) служебной шины.

Маркер [подписанного URL-адреса](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) содержит имя выбранного правила авторизации, универсальный код ресурса (URI), к которому запрашивается доступ, срок действия и криптографическую подпись HMAC-SHA256, вычисленную на основе этих полей с помощью первичного или вторичного криптографического ключа выбранного правила авторизации.

## <a name="shared-access-authorization-policies"></a>Политики авторизации общего доступа

Каждое пространство имен и каждая сущность служебной шины имеет политику авторизации общего доступа, которая состоит из правил. Политика на уровне пространства имен применяется ко всем сущностям в пространстве имен независимо от настройки их политики.

Для каждого правила политики авторизации необходимо выбрать три элемента информации: **имя**, **область** и **права**. **Имя** должно быть уникальным в пределах выбранной области. Область определяется с помощью URI соответствующего ресурса. Для пространства имен служебной шины область — это полное доменное имя (FQDN), такое как `https://<yournamespace>.servicebus.windows.net/`.

Правило политики может предоставлять следующие права:

* Отправка — предоставляет права на отправку сообщений в сущность.
* Прослушивание — предоставляет права на прослушивание (ретранслятору) или получение (очереди и подпискам) и все задания обработки сообщений.
* Управление — предоставляет права на управление топологией пространства имен, включая создание и удаление сущностей.

Право на управление предоставляет права на отправку и получение.

Политика пространства имен или сущности может содержать до 12 правил авторизации общего доступа, охватывающим три набора правил, каждое из которых охватывает основные права и сочетание прав на отправку и прослушивание. Это ограничение подчеркивает, что хранилище политики SAS не может использоваться как хранилище учетных записей пользователя или службы. Если для приложения необходимо предоставить доступ к служебной шине на основе удостоверений пользователя или службы, в нем необходимо реализовать службу маркеров безопасности, которая выдает маркеры SAS после выполнения проверки подлинности и проверки доступа.

Правилу авторизации назначается *первичный* и *вторичный ключ*. Это криптографически строгие ключи. Их нельзя потерять — они всегда будут доступны на [портал Azure][Azure portal]. Вы можете использовать любой из созданных ключей и создавать их повторно в любое время. Если вы повторно создадите или измените ключ в политике, все ранее выданные маркеры на его основе моментально станут недействительными. Тем не менее текущие подключения, созданные на основе этих маркеров, продолжат работать до истечения срока их действия.

Когда вы создаете пространство имен служебной шины, для него автоматически создается политика с именем **RootManageSharedAccessKey**. Эта политика содержит разрешения на управления для всего пространства имен. Рекомендуем обращаться с этим правилом как с учетной записью **суперпользователя** (администратора) и не использовать его в приложении. Вы можете создать дополнительные правила политики для пространства имен на вкладке **Настройка** портала, с помощью PowerShell или Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Настройка проверки подлинности подписанного URL-адреса

Правило [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) можно настроить в пространствах имен, очередях или разделах служебной шины. Настройка правила [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) в подписке на служебную шину сейчас не поддерживается, но для безопасного доступа к подпискам можно использовать правила, настроенные в пространстве имен или разделе. Рабочий пример, в котором показана эта процедура, см. в примере [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

![SAS](./media/service-bus-sas/service-bus-namespace.png)

На этом рисунке правила авторизации *manageRuleNS*, *sendRuleNS* и *listenRuleNS* применяются к очереди Q1 и разделу T1, правила *listenRuleQ* и *sendRuleQ* — только к очереди Q1 и правило *sendRuleT* — только к разделу T1.

## <a name="generate-a-shared-access-signature-token"></a>Создание маркера подписанного URL-адреса

Каждый клиент, который имеет доступ к имени правила авторизации и одному из его ключей подписи, может создать маркер SAS. Маркер создается путем составления строки в следующем формате:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** — срок действия маркера. Количество секунд с начала эпохи UNIX (`00:00:00 UTC` 1 января 1970 года) до истечения срока действия маркера.
* **`skn`** — имя правила авторизации.
* **`sr`** — URI ресурса, к которому осуществляется доступ.
* **`sig`** — подпись.

`signature-string` — это хэш SHA-256, вычисленный на основе URI ресурса (в значении **области**, описанном в предыдущем разделе), и строковое представление срока действия маркера, разделенное символом CRLF.

Вычисление хэша напоминает следующий псевдокод и возвращает 256-битное (32-байтное) значение хэша.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Маркер содержит нехэшируемые значения, что позволяет получателю повторно вычислить хэш с теми же параметрами и проверить, что издатель использует допустимый ключ подписи. 

URI ресурса — это полный URI ресурса служебной шины, к которому запрашивается доступ. Например, `http://<namespace>.servicebus.windows.net/<entityPath>` или `sb://<namespace>.servicebus.windows.net/<entityPath>`. Вся строка будет выглядеть так: `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. URI должен быть [закодирован с помощью знака процента](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Правило авторизации общего доступа, используемое для подписи, необходимо настроить для сущности, указанной данным URI или одной из ее иерархических родительских сущностей. Например, в предыдущем примере это — `http://contoso.servicebus.windows.net/contosoTopics/T1` или `http://contoso.servicebus.windows.net`.

Маркер SAS действителен для всех ресурсов с префиксом `<resourceURI>`, используемым в `signature-string`.

## <a name="regenerating-keys"></a>Повторное создание ключей

Рекомендуется периодически повторно создавать ключи, используемые в объекте [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Слоты первичного и вторичного ключей позволяют последовательно изменять ключи. Если ваше приложение обычно использует первичный ключ, вы можете скопировать его в слот вторичного ключа и только тогда повторно создать первичный ключ. Новое значение первичного ключа затем можно настроить в приложениях клиента, которые имеют постоянный доступ, используя старый первичный ключ в слоте вторичного. После обновления всех клиентов можно повторно создать вторичный ключ, чтобы окончательно прекратить использовать старый первичный ключ.

Если вы знаете или подозреваете, что ключ скомпрометирован и необходимо отозвать оба ключа, можно повторно создать ключи [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) и [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) в правиле [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), заменив ими старые. В результате этой процедуры все маркеры, подписанные старыми ключами, станут недействительными.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Проверка подлинности подписанного URL-адреса с помощью служебной шины

Описанные ниже сценарии включают настройку правил авторизации, создание маркеров SAS и авторизацию клиента.

Полностью рабочий пример приложения служебной шины, демонстрирующий настройку и использование авторизации SAS, см. в статье [Проверка подлинности подписанного URL-адреса с помощью служебной шины](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Сопутствующий пример, демонстрирующий использование правил авторизации SAS, настроенных в пространствах имен или разделах, для защиты подписок служебной шины, доступен здесь: [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Доступ к правилам авторизации общего доступа в сущности

С помощью библиотек .NET Framework служебной шины вы можете получить доступ к объекту [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), настроенному для очереди или раздела служебной шины, через коллекцию [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) в соответствующих объектах [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) и [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

Приложения, использующие пакет Azure SDK для .NET с библиотеками служебной шины для .NET, могут использовать авторизацию SAS с помощью класса [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . В следующем коде показано, как использовать поставщик маркеров, чтобы отправить сообщения в очередь служебной шины. Помимо указанных здесь вариантов вы также можете передать ранее выданный маркер в фабричный метод поставщика маркеров.

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

Вы также можете использовать поставщик маркеров, чтобы напрямую выдавать маркеры для передачи другим клиентам. 

Строки подключения могут включать имя правила (*SharedAccessKeyName*), ключ правила (*SharedAccessKey*) или ранее выданный маркер (*SharedAccessSignature*). Если они присутствуют в строке подключения, переданной в конструктор или фабричный метод, принимающий строку подключения, поставщик маркеров SAS создается и заполняется автоматически.

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

Если маркер SAS назначается отправителю или клиенту, они не могут ни получить ключ напрямую, ни зарезервировать хэш для его получения. Это позволяет контролировать, к чему вы предоставляете доступ и на какое время. Помните, что при изменении первичного ключа в политике любой созданный на ее основе подписанный URL-адрес станет недействительным.

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
> Важно, чтобы подключение было создано с применением **механизма проверки подлинности ANONYMOUS** (а не механизма по умолчанию PLAIN, при котором имя пользователя и пароль используются, когда не нужно отправлять маркер SAS).
> 
> 

Затем издатель создает две ссылки AMQP для отправки маркера SAS и получения ответа (результата проверки маркера) от службы.

Сообщение AMQP содержит набор свойств и больше информации по сравнению с простым сообщением. Маркер SAS составляет тело сообщения (с помощью конструктора). Свойству **ReplyTo** присваивается имя узла для получения результата проверки по ссылке получателя (при желании это имя можно изменить, и оно будет создано службой динамически). Последние три свойства приложения или пользовательских свойства используются службой, чтобы понять, какую операцию ей нужно выполнить. Как описано в черновой спецификации CBS, это должно быть **имя операции** (put-token), **тип маркера** (в этом случае `servicebus.windows.net:sastoken`) и, наконец, **"имя" аудитории**, к которой относится маркер (вся сущность).

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
| Планирование последующей доставки сообщений (например, [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_)) |Прослушивание | Любой допустимый адрес очереди
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

## <a name="next-steps"></a>Дополнительная информация

Дополнительную информацию об обмене сообщениями через служебную шину см. в следующих разделах.

* [Базовая информация о Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com