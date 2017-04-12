---
title: "Развертывание ресурсов с помощью REST API и шаблона | Документация Майкрософт"
description: "Узнайте, как использовать Azure Resource Manager и REST API Resource Manager для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: d2d9cff50d6ba5dc990140059536786fb7281827
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Развертывание ресурсов с использованием шаблонов и REST API Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
> * [Портал](resource-group-template-deploy-portal.md)
> * [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
> 
> 

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
1. Задайте [общие параметры и заголовки](https://docs.microsoft.com/rest/api/index), включая маркеры аутентификации.
2. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор подписки, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate).
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. Проверьте развернутую службу перед ее выполнением. Для этого выполните операцию [проверки развертывания шаблонов](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate). При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении (как показано на следующем шаге).
4. Создайте развертывание. Укажите идентификатор подписки, имя группы ресурсов, имя развертывания и ссылку на шаблон. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](#parameter-file). Чтобы больше узнать о REST API для создания группы ресурсов, ознакомьтесь с [созданием развертывания на основе шаблона](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Обратите внимание, что для параметра **mode** выбрано значение **Incremental**. Чтобы выполнить полное развертывание, установите для параметра **mode** значение **Complete**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
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
   
      Если вы хотите регистрировать в журнале содержимое запроса или содержимое ответа (или и то, и другое), добавьте в запрос параметр **debugSetting** .
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      Можно настроить учетную запись хранения для использования маркера подписанного URL-адреса (SAS). Дополнительные сведения см. в статье [Делегирование доступа с помощью подписанного URL-адреса](https://docs.microsoft.com/rest/api/storageservices/fileservices/delegating-access-with-a-shared-access-signature).
5. Получите состояние развертывания шаблона. Чтобы узнать больше, ознакомьтесь с [получением сведений о развертывании на основе шаблона](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>Файл параметров

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об обработке асинхронных операций REST см. в статье [Track asynchronous Azure operations](resource-manager-async-operations.md) (Отслеживание асинхронных операций Azure).
* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).
* Сведения об автоматизации развертывания см. в статье [Automating application deployments to Azure Virtual Machines](../virtual-machines/windows/dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Автоматизация развертывания приложений на виртуальных машинах Azure), которая входит в цикл из четырех частей. Этот цикл охватывает такие аспекты, как архитектура приложения, доступ и безопасность, доступность и масштабирование, а также развертывание приложения.


