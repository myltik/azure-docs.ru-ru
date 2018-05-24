---
title: Проверка соответствия политике центра безопасности с помощью REST API Azure | Документы Майкрософт
description: Сведения об использовании REST API Azure для проверки соответствия политикам центра безопасности.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077769"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Проверка соответствия политике центра безопасности с помощью REST API

Центр безопасности периодически проверяет ресурсы Azure на соответствие определенным политикам безопасности. С помощью REST API, доступного в центре безопасности, вы можете проверять соответствие требованиям из своих собственных приложений. Вы можете отправить прямой запрос к службе или импортировать результаты JSON в другие приложения. 

Здесь вы узнаете, как получить текущий набор рекомендаций из всех ресурсов Azure, связанных с подпиской.

Чтобы получить текущий набор рекомендаций:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Создание запроса  

Параметр `{subscription-id}` является обязательным и должен содержать идентификатор подписки для подписки Azure, определяющей политики. Если у вас несколько подписок, см. раздел [Использование нескольких подписок](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

Параметр `api-version` является обязательным. Сейчас эти конечные точки поддерживаются только для `api-version=2015-06-01-preview`. 

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Задайте в качестве значения [допустимый токен доступа](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

## <a name="response"></a>Ответ  

Код состояния 200 (ОК) возвращается в случае успешного ответа, который содержит список рекомендуемых задач для защиты ресурсов Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Каждый элемент в **value** представляет рекомендацию.

|Свойство ответа|ОПИСАНИЕ|
|----------------|----------|
|**state** | Указывает значение рекомендации — `active` или `resolved`. |
|**creationTimeUtc** | Дата и время (в формате UTC) создания рекомендации. |
|**lastStateChangeUtc** | Дата и время (в формате UTV) последнего изменения состояния, если таковое имеется. |
|**securityTaskParameters** | Сведения о рекомендации. Свойства зависят от базовой рекомендации. |
||
  
Перечень рекомендаций, поддерживаемых на данный момент, см. в статье [Реализация рекомендаций по вопросам безопасности](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Другие коды состояния означают состояния ошибки. В этих случаях объект ответа содержит описание, поясняющее причину сбоя запроса.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Пример ответа  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

В этом ответе показаны две рекомендации. Каждый элемент в списке соответствует конкретной рекомендации. Первая рекомендует шифрование хранилища на виртуальной машине Linux, а вторая предлагает включить аудит для SQL Server.

Рекомендации зависят от включенных политик. Дополнительные сведения о рекомендациях по вопросам безопасности см. в статье [Managing security recommendations in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) (Управление рекомендациями по вопросам безопасности в центре безопасности Azure).


## <a name="see-also"></a>См. также  
- [Установка политик безопасности](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [REST API поставщика ресурсов центра безопасности Azure](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Начало работы с Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
- [Модуль PowerShell для центра безопасности Azure](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
