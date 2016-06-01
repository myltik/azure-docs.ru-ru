<properties
   pageTitle="Устранение неполадок развернутых служб с помощью REST API | Microsoft Azure"
   description="Описывается использование REST API Azure Resource Manager для обнаружения и устранения проблем развертывания Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Устранение неполадок развертывания групп ресурсов с помощью REST API Azure Resource Manager

> [AZURE.SELECTOR]
- [Портал](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Интерфейс командной строки Azure](resource-manager-troubleshoot-deployments-cli.md)
- [ИНТЕРФЕЙС REST API](resource-manager-troubleshoot-deployments-rest.md)

Если вы получили ошибку при развертывании ресурсов в Azure, необходимо узнать, что пошло не так. REST API предоставляет операции, которые позволяют находить ошибки и определять возможные действия по их исправлению.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Устранение неполадок с API REST

1. Разверните ресурсы с помощью операции [создания развертывания шаблона](https://msdn.microsoft.com/library/azure/dn790564.aspx). Чтобы сохранить сведения, которые могут быть полезны для отладки, присвойте свойству **debugSetting** в запросе JSON значение **requestContent** и (или) **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    По умолчанию **debugSetting** имеет значение **none**. При указании значения **debugSetting** внимательно рассмотрите тип данных, передаваемых в ходе развертывания. При ведении журнала с информацией о запросе или ответе возможно раскрытие конфиденциальных данных, извлекаемых с помощью операций развертывания.

2. Получите сведения о развертывании с помощью операции [получения сведений о развертывании шаблона](https://msdn.microsoft.com/library/azure/dn790565.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    В ответе обратите особое внимание на элементы **provisioningState**, **correlationId** и **error**. **CorrelationId** используется для отслеживания связанных событий и может быть полезен при взаимодействии с технической поддержкой для устранения проблемы.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. Получите сведения об операциях развертывания с помощью операции [вывода списка всех операций развертывания шаблона](https://msdn.microsoft.com/library/azure/dn790518.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    Ответ будет содержать сведения о запросе и (или) ответе в зависимости от того, что было указано в свойстве **debugSetting** во время развертывания.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Получите события из журналов аудита для развертывания с помощью операции [перечисления событий управления в подписке](https://msdn.microsoft.com/library/azure/dn931934.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## Дальнейшие действия

- Сведения об устранении некоторых ошибок развертывания см. в разделе [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Чтобы узнать об использовании журналов аудита для отслеживания других типов действий, см. раздел [Операции аудита с помощью диспетчера ресурсов](resource-group-audit.md).
- Чтобы проверить развернутую службу перед ее выполнением, см. раздел [Развертывание группы ресурсов с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0518_2016-->