---
title: "Интерфейсы REST API Resource Manager | Документация Майкрософт"
description: "Обзор примеров проверки подлинности и использования API-интерфейсов REST диспетчера ресурсов"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="resource-manager-rest-apis"></a>API-интерфейсы REST диспетчера ресурсов
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
> * [Портал](resource-group-portal.md) 
> * [ИНТЕРФЕЙС REST API](resource-manager-rest-api.md)
> 
> 

Каждый вызов диспетчера ресурсов Azure, каждый развернутый шаблон, каждая настроенная учетная запись хранения связаны с одним или несколькими вызовами REST API в Azure Resource Manager. Эта статья посвящена таким API-интерфейсам и возможностям их вызова без использования пакетов SDK. Такой подход полезен, если требуется полный контроль всех запросов к Azure или если пакет SDK для предпочитаемого языка недоступен либо не поддерживает нужные операции.

В этой статье не будет рассматриваться каждый API, существующий в Azure, а только некоторые операции в качестве примера. Ознакомившись с основами, переходите к [справочнику по REST API в Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/), который предоставит подробные сведения об использовании остальных API-интерфейсов.

## <a name="authentication"></a>Аутентификация
Проверка подлинности для Resource Manager осуществляется с помощью Azure Active Directory (AD). Чтобы подключиться к любому API-интерфейсу, сначала нужно пройти аутентификацию в Azure AD для получения маркера аутентификации, который можно передавать в каждый запрос. Мы рассматриваем прямой вызов непосредственно к интерфейсам REST API, и в этом контексте вы вряд ли хотите видеть запрос на ввод имени пользователя и пароля. Кроме того, мы предполагаем, что двухфакторная проверка подлинности не используется. Поэтому мы создадим то, что называется приложением Azure AD, и субъект-службу, которые будут использоваться для входа. Помните, что Azure AD поддерживает несколько процедур проверки подлинности и все они могут использоваться для получения маркера проверки подлинности, необходимого для последующих запросов API.
Пошаговые инструкции см. в статье [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](resource-group-create-service-principal-portal.md).

### <a name="generating-an-access-token"></a>Создание маркера доступа
Проверка подлинности в Azure AD выполняется путем вызова службы Azure AD, находящейся по адресу login.microsoftonline.com. Чтобы выполнить аутентификацию, требуются следующие сведения:

* идентификатор клиента Azure AD (имя Azure AD, используемое для входа; обычно оно совпадает с названием компании, но не обязательно);
* идентификатор приложения (полученный на этапе создания приложения Azure AD);
* пароль (выбранный при создании приложения Azure AD).

В следующем HTTP-запросе замените "Azure AD Tenant ID", "Application ID" и "Password" правильными значениями.

**Базовый HTTP-запрос:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

Если аутентификация пройдет успешно, на этот запрос будет возвращен ответ такого вида:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
Значение параметра access_token в ответе выше сокращено для удобства чтения.

**Создание маркера доступа с помощью Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Создание маркера доступа с помощью PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

Ответ содержит маркер доступа, сведения о сроке его действия и о том, для каких ресурсов его можно использовать.
Маркер доступа, полученный в предыдущем вызове HTTP, следует передавать во всех запросах к API в Resource Manager. Он передается в заголовке с именем "Authorization", который должен иметь значение "Bearer полученный_маркер_доступа". Обратите внимание на пробел между словом "Bearer" и маркером доступа.

Как видно из приведенного выше ответа HTTP, для маркера определен период действия. Маркер следует сохранить в кэш и повторно использовать в течение всего этого периода. Несмотря на то, что можно проверить подлинность каждого вызова API в Azure AD, это будет крайне неэффективно.

## <a name="calling-resource-manager-rest-apis"></a>Вызов интерфейсов REST API в Resource Manager
В этом разделе упоминаются только несколько API-интерфейсов, на примере которых мы объясним основные принципы использования операций REST. Сведения о других операциях вы найдете в [справочнике по REST API в Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Вывод списка всех подписок
Одной из простейших операций является вывод списка доступных подписок, к которым вы можете обращаться. В следующем запросе можно увидеть, как маркер доступа передается в качестве заголовка.

(Замените YOUR_ACCESS_TOKEN фактическим маркером доступа.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

В ответе вы получите список подписок, к которым может получать доступ этот субъект-служба.

Идентификаторы подписок сокращены для удобства чтения.

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Вывод списка всех групп ресурсов в конкретной подписке
Все ресурсы, доступные через API-интерфейсы Resource Manager, входят в группу ресурсов. Список групп ресурсов, существующих в подписке, можно получить из Resource Manager с помощью приведенного ниже запроса HTTP GET. Обратите внимание, что в этом запросе идентификатор подписки передается в составе URL-адреса.

Замените YOUR_ACCESS_TOKEN фактическим маркером доступа, а SUBSCRIPTION_ID — идентификатором подписки.

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Ответ зависит от того, определены ли в подписке группы ресурсов, и сколько их.

Идентификаторы подписок сокращены для удобства чтения.

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Пока мы обращались к API-интерфейсам Resource Manager только с запросами на получение информации. Теперь давайте попробуем создать какие-то ресурсы. Проще всего создать группу ресурсов. Следующий запрос HTTP создает группу ресурсов в указанном регионе или расположении и добавляет к ней тег.

Замените YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME фактическим маркером доступа, идентификатором подписки и именем группы ресурсов, которую вы планируете создать.

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

В случае успешного выполнения отобразится ответ, который выглядит примерно так:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Вы успешно создали группу ресурсов в Azure. Поздравляем!

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Развертывание ресурсов в группе ресурсов с помощью шаблона Resource Manager
При работе с Resource Manager ресурсы можно развертывать с помощью шаблонов. Шаблон определяет несколько ресурсов и соответствующие зависимости. В этом разделе мы предполагаем, что вы уже знакомы с шаблонами Resource Manager, поэтому сразу перейдем к вызову API для запуска развертывания. Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).

Развертывание шаблона сильно не отличается от вызова других API-интерфейсов. Но важной особенностью является то, что развертывание шаблона может занять много времени. Вызов API просто завершается, а дальше вы как разработчик можете реализовать проверку статуса развертывания, чтобы выяснить, когда оно завершится. Дополнительные сведения см. в разделе [Отслеживание асинхронных операций Azure](resource-manager-async-operations.md).

В этом примере используется общедоступный шаблон из репозитория [GitHub](https://github.com/Azure/azure-quickstart-templates). Этот шаблон развертывает виртуальную машину Linux в западной части США. В нашем примере используется ссылка на шаблон, размещенный в общедоступном репозитории, в данном случае GitHub. Вместо этого вы можете передать сам шаблон в тексте запроса. Обратите внимание, что в запросе мы передаем значения параметров, которые будут использоваться внутри шаблона.

Замените SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD и DNS_NAME_FOR_PUBLIC_IP значениями, соответствующими вашему запросу.

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

Для удобства чтения статьи мы опустили довольно длинный ответ JSON на данный запрос. Этот ответ содержит сведения о развертывании, созданном на основе шаблона.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об обработке асинхронных операций REST см. в статье [Отслеживание асинхронных операций Azure](resource-manager-async-operations.md).
