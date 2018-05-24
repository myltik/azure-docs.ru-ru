---
title: 'Сетка событий Azure: безопасность и проверка подлинности'
description: В статье описываются сетка событий Azure и ее основные понятия.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: babanisa
ms.openlocfilehash: 783766c3e12da2c6fd77f919cf0ec44aea7db3b7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-security-and-authentication"></a>Сетка событий: безопасность и проверка подлинности 

В сетке событий Azure предусмотрено три типа проверки подлинности:

* Подписки на события
* Публикация событий
* Доставка событий веб-перехватчика

## <a name="webhook-event-delivery"></a>Доставка событий веб-перехватчика

Веб-перехватчики — это один из многих способов получения событий из службы "Сетка событий Azure". При возникновении нового события служба "Сетка событий" отправляет в конечную точку веб-перехватчика HTTP-запрос, в теле которого находится событие.

Когда вы регистрируете собственную конечную точку веб-перехватчика с помощью службы "Сетка событий", отправляется запрос POST с кодом простой проверки, чтобы подтвердить владение конечной точкой. В ответ приложение должно вернуть код проверки. Служба "Сетка событий" не доставляет события в конечные точки веб-перехватчика, которые не прошли проверку. При использовании службы API сторонних производителей (например, [Zapier](https://zapier.com) или [IFTTT](https://ifttt.com/)), возможно, не удастся вывести на экран код проверки программными средствами. Для этих служб можно вручную проверить подписку с помощью URL-адреса проверки, который отправляется в событии проверки подписки. Скопируйте этот URL-адрес и отправьте запрос GET через клиент REST или веб-браузер.

Возможность выполнения проверки вручную реализована предварительной версии. Чтобы использовать ее, нужно установить [расширение службы "Сетка событий"](/cli/azure/azure-cli-extensions-list) для [AZ CLI 2.0](/cli/azure/install-azure-cli). Расширение можно установить с помощью `az extension add --name eventgrid`. Если вы используйте REST API, убедитесь, что установлена версия `api-version=2018-05-01-preview`.

### <a name="validation-details"></a>Сведения о проверке

* Во время создания или обновления подписки на событие сетка событий публикует на целевой конечной точке событие "SubscriptionValidationEvent".
* В качестве заголовка событие содержит значение "Aeg-Event-Type: SubscriptionValidation".
* Текст события имеет ту же схему, что и другие события сетки событий.
* В данных события содержится свойство ValidationCode со строкой, сгенерированной случайным образом. Например "validationCode: acb13…".
* Данные о событии включают свойство validationUrl с URL-адресом для проверки подписки вручную.
* Массив содержит только событие проверки. Другие события отправляются в отдельном запросе после возврата кода проверки.

Пример SubscriptionValidationEvent показан в следующем примере:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Чтобы подтвердить владение конечной точкой, в свойстве validationResponse возвращается код проверки, как показано в следующем примере:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Также можно вручную проверить подписку, отправив запрос GET по URL-адресу проверки. Подписка на событие остается в состоянии ожидания, пока проверка не будет завершена.

### <a name="event-delivery-security"></a>Защита доставки событий

Защитить конечную точку веб-перехватчика можно путем добавления параметров запроса для URL-адреса веб-перехватчика при создании подписки на события. Задайте один из этих параметров запроса в качестве секрета (например, [маркер доступа](https://en.wikipedia.org/wiki/Access_token)), с помощью которого веб-перехватчик сможет распознавать событие, поступающее из Сетки событий с допустимыми разрешениями. Сетка событий будет включать эти параметры в каждую доставку событий для веб-перехватчика.

При изменении подписки на событие параметры запроса не будут отображаться или возвращаться, если в [интерфейсе командной строки](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) Azure не используется параметр [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_show) (включить полный URL-адрес конечной точки).

Наконец, необходимо отметить, что служба "Сетка событий Azure" поддерживает только конечные точки веб-перехватчиков HTTPS.

## <a name="event-subscription"></a>Подписка на события

Чтобы подписаться на событие, необходимо иметь разрешение **Microsoft.EventGrid/EventSubscriptions/Write** для требуемого ресурса. Это разрешение необходимо, так как вы записываете новую подписку в области действия ресурса. Требуемый ресурс зависит от того, на какой раздел оформляется подписка: системный или пользовательский. В этом разделе описываются оба типа подписки.

### <a name="system-topics-azure-service-publishers"></a>Системные разделы (издатели служб Azure)

Для системных разделов необходимо разрешение на запись новой подписки на события в области действия ресурса, публикующего событие. Ресурс имеет следующий формат: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Например, чтобы подписаться на событие в учетной записи хранения с именем **myacct**, требуется разрешение Microsoft.EventGrid/EventSubscriptions/Write для следующего ресурса: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Пользовательские разделы

Для пользовательских разделов необходимо разрешение на запись новой подписки на события в области действия для сетки событий. Ресурс имеет следующий формат: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Например, чтобы подписаться на пользовательский раздел с именем **mytopic**, требуется разрешение Microsoft.EventGrid/EventSubscriptions/Write для следующего ресурса: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Публикация разделов

Для разделов используется либо подписанный URL-адрес (SAS), либо проверка подлинности с использованием ключа. Рекомендуется использовать SAS, но проверка подлинности с использованием ключа обеспечивает простое программирование, а также совместима со множеством существующих издателей веб-перехватчиков. 

Значение проверки подлинности следует включить в заголовок HTTP. Для SAS в качестве значения заголовка используйте **aeg-sas-token**. Для подлинности с использованием ключа в качестве значения заголовка следует использовать **aeg-sas-key**.

### <a name="key-authentication"></a>Проверка подлинности с использованием ключа

Проверка подлинности с использованием ключа — самая простая форма проверки подлинности. Используйте следующий формат: `aeg-sas-key: <your key>`

Например, для передачи ключа можно использовать следующую команду:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Маркеры SAS

Маркеры SAS для сетки события включают ресурс, срок его действия и подпись. Маркер SAS имеет следующий формат: `r={resource}&e={expiration}&s={signature}`.

Ресурс — это путь для раздела сетки событий, в который вы отправляете события. Вот пример допустимого пути к ресурсу: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Вы создаете подпись на основе ключа.

Например, допустимое значение **aeg-sas-token** выглядит следующим образом:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

В следующем примере создается маркер SAS для использования с сеткой событий:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Контроль доступа к управлению

Сетка событий Azure позволяет контролировать уровень доступа, предоставленного разным пользователям для выполнения различных операций управления, таких как создание списка подписок на события, создание новых подписок и генерирование ключей. В службе "Сетка событий" используется проверка доступа на основе ролей Azure (RBAC).

### <a name="operation-types"></a>Типы операций

Сетка событий Azure поддерживает следующие действия:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Последние три операции возвращают потенциально секретную информацию, которая отфильтровывается из обычных операций чтения. Это наилучший способ ограничить доступ к этим операциям. Настраиваемые роли можно создавать с помощью [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md), [интерфейса командной строки (CLI) Azure](../role-based-access-control/role-assignments-cli.md) и интерфейса [REST API](../role-based-access-control/role-assignments-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Применение проверки доступа на основе ролей (RBAC)

Чтобы принудительно применить RBAC для разных пользователей, выполните следующие действия:

#### <a name="create-a-custom-role-definition-file-json"></a>Создание файла определения пользовательской роли (.json)

Вот пример определений роли сетки событий, позволяющие пользователям выполнять разные действия.

**EventGridReadOnlyRole.json**: разрешено только для операций чтения.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: разрешен ограниченный набор действий по публикации и запрещены действия удаления.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: разрешено выполнять все действия сетки событий.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Создание и назначение пользовательской роли с помощью Azure CLI

Чтобы создать настраиваемую роль, используйте следующую команду:

```azurecli
az role definition create --role-definition @<file path>
```

Чтобы назначить роль пользователю, используйте следующую команду:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о сетке событий см. в статье [Сведения о сетке событий](overview.md)
