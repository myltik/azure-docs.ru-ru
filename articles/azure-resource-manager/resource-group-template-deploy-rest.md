---
title: Развертывание ресурсов с помощью REST API и шаблона | Документация Майкрософт
description: Узнайте, как использовать Azure Resource Manager и REST API Resource Manager для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 6ae77eb1f619928f43a502cd4631a0895a9e91f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603746"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Развертывание ресурсов с использованием шаблонов и REST API Resource Manager

В этом разделе объясняется, как использовать шаблоны и REST API Resource Manager для развертывания ресурсов в Azure.  

> [!TIP]
> Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
> 
> * Статья [Просмотр операций развертывания с помощью Azure Resource Manager](resource-manager-deployment-operations.md) содержит информацию о том, как получить сведения, которые помогут устранить ошибку.
> * [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md) — руководство по устранению распространенных ошибок при развертывании.
> 
> 

Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Развертывание с помощью REST API
1. Задайте [общие параметры и заголовки](/rest/api/azure/), включая маркеры аутентификации.

2. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор подписки, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. Проверьте развернутую службу перед ее выполнением. Для этого выполните операцию [проверки развертывания шаблонов](/rest/api/resources/deployments/validate). При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении (как показано на следующем шаге).

4. Создайте развертывание. Укажите идентификатор подписки, имя группы ресурсов, имя развертывания и ссылку на шаблон. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](#parameter-file). Чтобы больше узнать о REST API для создания группы ресурсов, ознакомьтесь с [созданием развертывания на основе шаблона](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Обратите внимание, что для параметра **mode** выбрано значение **Incremental**. Чтобы выполнить полное развертывание, установите для параметра **mode** значение **Complete**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    Если вы хотите регистрировать в журнале содержимое запроса или содержимое ответа (или и то, и другое), добавьте в запрос параметр **debugSetting** .

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    Можно настроить учетную запись хранения для использования маркера подписанного URL-адреса (SAS). Дополнительные сведения см. в статье [Делегирование доступа с помощью подписанного URL-адреса](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

5. Получите состояние развертывания шаблона. Чтобы узнать больше, ознакомьтесь с [получением сведений о развертывании на основе шаблона](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Для неудачных развертываний можно задать автоматическое повторное выполнение более ранних развертываний, зафиксированных в журнале. Для этого развертывания должны иметь уникальные имена, чтобы их можно было идентифицировать в журнале. Если у вас нет уникальных имен, текущее неудачное развертывание может перезаписать более раннее успешное развертывание, зафиксированное в журнале. Этот параметр можно использовать только с развертываниями корневого уровня. Повторное развертывание из вложенных шаблонов не поддерживается.

Чтобы повторно выполнить последнее успешное развертывание, если текущее развертывание завершается сбоем, используйте следующий код:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Чтобы повторно выполнить определенное развертывание, если текущее развертывание завершается сбоем, используйте следующий код:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

Указанное развертывание должно быть успешным.

## <a name="parameter-file"></a>Файл параметров

Если вы используете файл параметров для передачи значений параметров во время развертывания, то необходимо создать JSON-файл с форматом, как в примере ниже.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

Размер файла параметров не может быть более 64 КБ.

Если требуется предоставить конфиденциальное значение для параметра (например, пароль), добавьте это значение в хранилище ключей. Получите хранилище ключей во время развертывания, как показано в предыдущем примере. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Дополнительная информация
* Сведения об обработке асинхронных операций REST см. в статье [Track asynchronous Azure operations](resource-manager-async-operations.md) (Отслеживание асинхронных операций Azure).
* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).

