<properties 
    pageTitle="Проверка подлинности подписанного URL-адреса с помощью служебной шины | Microsoft Azure"
    description="Сведения о проверке подлинности SAS с помощью служебной шины."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/22/2016"
    ms.author="sethm" />

# Проверка подлинности подписи при общем доступе с помощью служебной шины

Проверка подлинности [подписанного URL-адреса (SAS)](service-bus-sas-overview.md) позволяет проверить подлинность приложений в служебной шине с помощью ключа доступа, настроенного в пространстве имен, или сущности сообщения (очереди или раздела), с которой связаны определенные права. Этот ключ можно использовать для создания маркера SAS, который клиенты могут использовать при проверке подлинности в служебной шине.

Поддержка проверки подлинности SAS включена в пакет Azure SDK версии 2.0 и выше. Дополнительные сведения о проверке подлинности с помощью служебной шины см. в статье [Проверка подлинности и авторизация с помощью служебной шины](service-bus-authentication-and-authorization.md).

## Основные понятия

Проверка подлинности SAS в служебной шине предусматривает настройку соответствующих прав для криптографического ключа в ресурсе служебной шины. Клиенты запрашивают доступ к ресурсам служебной шины, предоставляя маркер SAS. В маркере указывается URI ресурса, к которому требуется доступ, и срок действия. Маркер подписывается настроенным ключом.

Вы можете настраивать правила авторизации подписанного URL-адреса для [ретрансляторов](service-bus-fundamentals-hybrid-solutions.md#relays), [очередей](service-bus-fundamentals-hybrid-solutions.md#queues), [разделов](service-bus-fundamentals-hybrid-solutions.md#topics) и [концентраторов событий](service-bus-fundamentals-hybrid-solutions.md#event-hubs) служебной шины.

При проверке подлинности SAS используются следующие элементы.

- [Правило авторизации общего доступа](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): 256-битный основной криптографический ключ в кодировке Base64, необязательный дополнительный ключ, имя ключа и соответствующие права (набор прав на *прослушивание*, *отправку* и *управление*).

- Маркер [подписанного URL-адреса](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx): создается с помощью кода HMAC-SHA256 строки ресурса и криптографического ключа. В строке ресурса указывается URI ресурса, к которому запрашивается доступ, и срок действия. Подпись и другие элементы, описанные в следующих разделах, преобразуются в строку, образуя маркер [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx).

## Настройка проверки подлинности подписанного URL-адреса

Вы можете настроить правило [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) в пространствах имен, очередях или разделах служебной шины. Настройка правила [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) для подписки служебной шины в настоящее время не поддерживается. Тем не менее, чтобы получить безопасный доступ к подпискам, вы можете использовать правила, настроенные для пространств имен или разделов. Рабочий пример, иллюстрирующий эту процедуру, см. в примере [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

В пространстве имен, очереди или разделе служебной шины можно настроить до 12 таких правил. Правила, настроенные для пространства имен служебной шины, применяются ко всем сущностям в этом пространстве.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

На рисунке видно, что правила авторизации *manageRuleNS*, *sendRuleNS* и *listenRuleNS* применяются к очереди Q1 и разделу T1. Правила *listenRuleQ* и *sendRuleQ* применяются только к очереди Q1, а *sendRuleT* — только к разделу T1.

Основные параметры правила [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx).

|Параметр|Описание|
|---|---|
|*KeyName*|Строка с описанием правила авторизации.|
|*PrimaryKey*|256-битный первичный ключ в кодировке Base64 для подписи и проверки маркера SAS.|
|*SecondaryKey*|256-битный вторичный ключ в кодировке Base64 для подписи и проверки маркера SAS.|
|*AccessRights*|Список прав доступа, предоставляемых правилом авторизации. Эти права могут быть любым набором прав на прослушивание, отправку и управление.|

Когда пространство имен служебной шины подготовлено, по умолчанию создается правило [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), в котором параметру **RootManageSharedAccessKey** присваивается значение [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx).

## Повторное создание и отзыв ключей из правил авторизации общего доступа

Рекомендуется периодически повторно создавать ключи, используемые в объекте [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Чтобы создать маркер SAS, в приложениях рекомендуется использовать параметр [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx). При повторном создании ключей следует заменить параметр [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) старым первичным ключом. Затем нужно создать новый первичный ключ. Это позволит и дальше использовать для авторизации маркеры, содержащие как старый первичный ключ, так и тот, срок действия которого еще не истек.

В случае взлома, когда отзываются оба ключа, можно повторно создать ключи [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) и [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) в правиле [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), заменив ими старые. В результате этой процедуры все маркеры, подписанные старыми ключами, станут недействительными.

## Создание маркера подписанного URL-адреса

Любой клиент, у которого есть доступ к ключам подписи, указанным в правиле авторизации общего доступа, может создать маркер SAS. Он имеет следующий формат:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

**Подпись** для маркера SAS вычисляется с помощью хэш-кода HMAC-SHA256 строки для подписи с помощью свойства [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) правила авторизации. В строке для подписи указан URI ресурса и срок действия. Строка имеет следующий формат:

```
StringToSign = <resourceURI> + "\n" + expiry;
```

Обратите внимание, что для этой операции вам следует использовать зашифрованный URI ресурса. URI ресурса — это полный URI ресурса служебной шины, к которому запрашивается доступ. Например, `http://<namespace>.servicebus.windows.net/<entityPath>` или `sb://<namespace>.servicebus.windows.net/<entityPath>`. Вся строка будет выглядеть так: `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

Срок действия исчисляется в количестве секунд с момента начала эры Unix, т. е. с 00:00:00 1 января 1970 г. (в формате UTC).

Правило авторизации общего доступа, используемое для подписи, необходимо настроить для сущности, указанной данным URI или одной из ее иерархических родительских сущностей. Например, в предыдущем примере это — `http://contoso.servicebus.windows.net/contosoTopics/T1` или `http://contoso.servicebus.windows.net`.

Маркер SAS действителен для всех ресурсов по адресу `<resourceURI>`, используемому в строке для подписи.

Параметр [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) в маркере SAS ссылается на **keyName** правила авторизации общего доступа, используемого для создания маркера.

Параметр *URL-encoded-resourceURI* должен совпадать с URI, который используется в строке для подписи во время вычисления подписи. Значение должно быть [закодировано с помощью знака процента](https://msdn.microsoft.com/library/4fkewx0t.aspx).

## Использование проверки подлинности подписанного URL-адреса в служебной шине

Следующие сценарии включают настройку правил авторизации, создание маркеров SAS и авторизацию клиента.

Полностью рабочий пример приложения служебной шины, демонстрирующий настройку и использование авторизации SAS, см. в статье [Проверка подлинности подписанного URL-адреса в служебной шине](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Связанный пример, демонстрирующий использование правил авторизации SAS для пространств имен или разделов, обеспечивающее защиту подписок служебной шины, см. в статье [Использование проверки подлинности подписанного URL-адреса (SAS) для доступа к подпискам служебной шины](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## Доступ к правилам авторизации общего доступа в пространстве имен

Операции с корнем пространства имен служебной шины требуют проверки подлинности сертификата. Вам нужно передать сертификат управления для своей подписки Azure. Чтобы передать сертификат управления, нажмите кнопку **Параметры** на левой панели [классического портала Azure][]. Дополнительные сведения о сертификатах управления Azure см. в [обзоре сертификатов Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Конечная точка для доступа к правилам авторизации общего доступа в пространстве имен служебной шины выглядит следующим образом:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Чтобы создать объект [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) в пространстве имен служебной шины, выполните операцию POST в конечной точке с сериализацией информации о правиле в формат JSON или XML. Например:

```
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

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## Доступ к правилам авторизации общего доступа в сущности

Вы можете получить доступ к объекту [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), настроенному в очереди или разделе служебной шины, с помощью набора правил [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) в соответствующих объектах [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx), [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) или [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx).

В следующем коде показано, как добавлять правила авторизации для очереди.

```
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

## Использование авторизации подписанного URL-адреса

Приложения, использующие пакет Azure SDK для .NET с библиотеками служебной шины для .NET, могут использовать авторизацию SAS с помощью класса [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx). В следующем коде показано, как использовать поставщик маркеров, чтобы отправить сообщения в очередь служебной шины.

```
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

Обратите внимание, что для использования авторизации SAS с ретрансляторами служебной шины вы можете использовать ключи SAS, настроенные в пространстве имен служебной шины. Если вы явным образом создаете ретранслятор в объекте пространства имен ([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) с [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)), вы можете задать правила SAS для этого ретранслятора. Чтобы выполнить авторизацию SAS с помощью подписок служебной шины, вы можете использовать ключи SAS, настроенные в пространстве имен или разделе служебной шины.

## Права, необходимые для операций служебной шины

В следующей таблице показаны права доступа, необходимые для выполнения различных операций с ресурсами служебной шины.

|Операция|Требуемое утверждение|Область утверждения|
|---|---|---|
|**Пространство имен**|||
|Настройка правила авторизации для пространства имен|Управление|Любой адрес пространства имен|
|**Регистр служб**|||
|Перечисление частных политик|Управление|Любой адрес пространства имен|
|Передача|||
|Начало прослушивания в пространстве имен|Прослушивание|Любой адрес пространства имен|
|Отправка сообщений в прослушиватель в пространстве имен|Отправка|Любой адрес пространства имен|
|**Очередь**|||
|Создание очереди|Управление|Любой адрес пространства имен|
|Удаление очереди|Управление|Любой допустимый адрес очереди|
|Перечисление очередей|Управление|/$Resources/Queues|
|Получение описания очереди|Управление или отправка|Любой допустимый адрес очереди|
|Настройка правила авторизации для очереди|Управление|Любой допустимый адрес очереди|
|Отправка в очередь|Отправка|Любой допустимый адрес очереди|
|Получение сообщений из очереди|Прослушивание|Любой допустимый адрес очереди|
|Прерывание или завершение обмена сообщениями после получения сообщения в режиме чтения с блокировкой|Прослушивание|Любой допустимый адрес очереди|
|Откладывание сообщения для последующего получения|Прослушивание|Любой допустимый адрес очереди|
|Назначение сообщению статуса недоставленного|Прослушивание|Любой допустимый адрес очереди|
|Получение состояния, связанного с сеансом очереди сообщений|Прослушивание|Любой допустимый адрес очереди|
|Назначение состояния, связанного с сеансом очереди сообщений|Прослушивание|Любой допустимый адрес очереди|
|**Раздел**|||
|Создание раздела|Управление|Любой адрес пространства имен|
|Удаление раздела|Управление|Любой допустимый адрес раздела|
|Перечисление разделов|Управление|/$Resources/Topics|
|Получение описания раздела|Управление или отправка|Любой допустимый адрес раздела|
|Настройка правила авторизации для раздела|Управление|Любой допустимый адрес раздела|
|Отправка в раздел|Отправка|Любой допустимый адрес раздела|
|**Подписка**|||
|Создание подписки|Управление|Любой адрес пространства имен|
|Удаление подписки|Управление|../myTopic/Subscriptions/mySubscription|
|Перечисление подписок|Управление|../myTopic/Subscriptions|
|Получение описания подписки|Управление или прослушивание|../myTopic/Subscriptions/mySubscription|
|Прерывание или завершение обмена сообщениями после получения сообщения в режиме чтения с блокировкой|Прослушивание|../myTopic/Subscriptions/mySubscription|
|Откладывание сообщения для последующего получения|Прослушивание|../myTopic/Subscriptions/mySubscription|
|Назначение сообщению статуса недоставленного|Прослушивание|../myTopic/Subscriptions/mySubscription|
|Получение состояния, связанного с сеансом раздела|Прослушивание|../myTopic/Subscriptions/mySubscription|
|Назначение состояния, связанного с сеансом раздела|Прослушивание|../myTopic/Subscriptions/mySubscription|
|**Правило**|||
|Создание правила|Управление|../myTopic/Subscriptions/mySubscription|
|Удаление правила|Управление|../myTopic/Subscriptions/mySubscription|
|Перечисление правил|Управление или прослушивание|../myTopic/Subscriptions/mySubscription/Rules|
|**Центры уведомлений**|||
|Создание концентратора уведомлений|Управление|Любой адрес пространства имен|
|Создание или обновление регистрации активного устройства|Прослушивание или управление|../notificationHub/tags/{tag}/registrations|
|Обновление информации PNS|Прослушивание или управление|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|Отправка в центр уведомлений|Отправка|../notificationHub/messages|

## Дальнейшие действия

Более подробные сведения об использовании SAS в служебной шине см. в статье [Подписанные URL-адреса](service-bus-sas-overview.md).

Дополнительные сведения об аутентификации при доступе к служебной шине см. в статье [Аутентификация и авторизация в служебной шине](service-bus-authentication-and-authorization.md).

[классического портала Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0622_2016-->