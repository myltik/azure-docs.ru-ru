<properties
   pageTitle="API-интерфейсы REST диспетчера ресурсов| Microsoft Azure"
   description="Обзор примеров проверки подлинности и использования API-интерфейсов REST диспетчера ресурсов"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# API-интерфейсы REST диспетчера ресурсов

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
- [Портал](./azure-portal/resource-group-portal.md)
- [ИНТЕРФЕЙС REST API](resource-manager-rest-api.md)

Каждый вызов диспетчера ресурсов Azure, каждый развернутый шаблон, каждая настроенная учетная запись хранения связаны с одним или несколькими вызовами API RESTful диспетчера ресурсов Azure. Эта статья посвящена таким API-интерфейсам и возможностям их вызова без использования пакетов SDK. Это может быть очень полезно, если требуется полный контроль всех запросов к Azure, или если пакет SDK для предпочитаемого языка недоступен или не поддерживает операции, которые нужно выполнить.

В этой статье не будет рассматриваться каждый API, существующий в Azure. Однако некоторые из них будут использоваться в качестве примера. Если вы знакомы с основами, можете прочесть [справочник по REST API Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx), чтобы получить подробные сведения об использовании остальных API-интерфейсов.

## Аутентификация
Проверку подлинности для ARM обрабатывает Azure Active Directory (AD). Чтобы подключиться к любому API, сначала необходимо пройти проверку подлинности в Azure AD для получения маркера проверки подлинности, который можно передать в каждый запрос. Поскольку здесь описывается вызов непосредственно REST API, предполагается, что не требуется выполнять проверку подлинности с помощью обычного имени пользователя и пароля, когда во всплывающем окне может появиться запрос на ввод имени пользователя и пароля, и, возможно, даже использовать другие механизмы проверки подлинности, применяемые в сценариях двухфакторной проверки подлинности. Поэтому мы создадим приложение Azure AD и субъект-службу, которая будет использоваться для входа. Помните, что Azure AD поддерживает несколько процедур проверки подлинности и все они могут использоваться для получения маркера проверки подлинности, необходимого для последующих запросов API. Пошаговые инструкции см. в статье [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](./resource-group-create-service-principal-portal.md).

### Создание маркера доступа 
Проверка подлинности в Azure AD выполняется путем вызова службы Azure AD, находящейся по адресу login.microsoftonline.com. Чтобы выполнить проверку подлинности, требуются следующие сведения:

* идентификатор клиента Azure AD (имя Azure AD, используемое для входа, часто, но необязательно совпадающее с названием компании);
* идентификатор приложения (полученный на этапе создания приложения Azure AD);
* пароль (выбранный при создании приложения Azure AD).

В приведенном ниже HTTP-запросе замените "Azure AD Tenant ID", "Application ID" и "Password" правильными значениями.

**Базовый HTTP-запрос:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... приведет (в случае успешной проверки подлинности) к выводу запроса, аналогичного следующему:

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
(access\_token в ответе выше был сокращен для удобства чтения.)

**Создание маркера доступа с помощью Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Создание маркера доступа с помощью PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

Ответ содержит маркер доступа, сведения о сроке действия маркера и о том, для каких ресурсов его можно использовать. Маркер доступа, полученный в предыдущем вызове HTTP, должен быть передан для всех запросов API ARM в качестве заголовка с именем "Authorization" и значением "Bearer YOUR\_ACCESS\_TOKEN". Обратите внимание на пробел между "Bearer" и маркером доступа.

Как видно из приведенного выше результата HTTP, маркер действителен в течение определенного периода времени, когда следует кэшировать и повторно использовать этот же маркер. Несмотря на то, что можно проверить подлинность каждого вызова API в Azure AD, это будет крайне неэффективно.

## Вызов API-интерфейсов REST ARM

[Интерфейсы REST API Azure Resource Manager описаны здесь](https://msdn.microsoft.com/library/azure/dn790568.aspx). В этом руководстве не рассматривается использование каждого из них. Мы обратимся только к нескольким API-интерфейсам, чтобы объяснить основные принципы использования API. После этого вы сможете ознакомиться с официальной документацией.

### Вывод списка всех подписок

Одной из простейших операций является вывод списка доступных подписок, к которым вы можете обращаться. В запросе ниже можно увидеть передачу маркера доступа в качестве заголовка.

(Замените YOUR\_ACCESS\_TOKEN фактическим маркером доступа.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

В результате вы получите список подписок, к которым может получать доступ этот субъект-служба.

(Приведенные ниже идентификаторы подписок были сокращены для удобства чтения.)

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

### Вывод списка всех групп ресурсов в конкретной подписке

Все ресурсы, доступные с помощью API-интерфейсов ARM, входят в группу ресурсов. Отправим запрос к ARM на получение существующих групп ресурсов в подписке с помощью приведенного ниже HTTP-запроса GET. Обратите внимание, что в этот раз идентификатор подписки передается в составе URL-адреса.

(Замените YOUR\_ACCESS\_TOKEN и SUBSCRIPTION\_ID фактическим маркером доступа и идентификатором подписки.)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Полученный ответ зависит от того, были ли определены группы ресурсов, и если да, то сколько.

(Приведенные ниже идентификаторы подписок были сокращены для удобства чтения.)

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

### Создание группы ресурсов

Итак, мы запросили API-интерфейсы ARM на предоставление сведений. Теперь создадим несколько ресурсов и начнем с самого простого — с группы ресурсов. Следующий HTTP-запрос создает группу ресурсов в выбранном регионе или расположении и добавляет в нее один или несколько тегов (в примере ниже добавляется только один тег).

(Замените YOUR\_ACCESS\_TOKEN, SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME фактическим маркером доступа, идентификатором подписки и именем группы ресурсов, которую вы планируете создать.)

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

В случае успешного выполнения возвращается ответ, аналогичный приведенному.

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

### Развертывание ресурсов в группе ресурсов с помощью шаблона ARM

При работе с ARM можно развертывать ресурсы с помощью шаблонов ARM. Шаблон ARM определяет несколько ресурсов и их зависимостей. В этом разделе предполагается, что вы знакомы с шаблонами ARM, и мы просто покажем, как выполнить вызов API, чтобы начать развертывание. Подробную документацию по шаблонам ARM можно найти здесь.

Развертывание шаблона ARM практически не отличается от вызова других API-интерфейсов. Одним важным аспектом является то, что в зависимости от содержимого шаблона его развертывание может занять довольно много времени, а вызов API выполнит только возврат. Вы как разработчик должны запросить состояние развертывания, чтобы выяснить, когда оно завершится.

В этом примере будет использоваться открытый шаблон ARM, доступный в [GitHub](https://github.com/Azure/azure-quickstart-templates). Этот шаблон развернет виртуальную машину Linux в западной части США. Несмотря на то, что этот шаблон будет доступен в общедоступном репозитории, например GitHub, можно также передать полный шаблон в качестве части запроса. Обратите внимание, что мы предоставляем значения параметров в составе запроса, который будет использоваться внутри шаблона.

(Замените SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME, DEPLOYMENT\_NAME, YOUR\_ACCESS\_TOKEN, GLOBALY\_UNIQUE\_STORAGE\_ACCOUNT\_NAME, ADMIN\_USER\_NAME, ADMIN\_PASSWORD и DNS\_NAME\_FOR\_PUBLIC\_IP значениями, соответствующими вашему запросу.)

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

Для удобства чтения этой документации мы опустили довольно длинный ответ JSON на данный запрос. Ответ будет содержать сведения о только что созданном развертывании на основе шаблона.

<!---HONumber=AcomDC_0921_2016-->