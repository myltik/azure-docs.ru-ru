---
title: Операции развертывания с помощью Azure Resource Manager | Документация Майкрософт
description: Сведения о просмотре операций развертывания Azure Resource Manager с помощью портала, PowerShell, Azure CLI и REST API.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 04/23/2018
ms.author: tomfitz
ms.openlocfilehash: 523ea3bf5d41231ab3281f9d8eb1fac8c3dfb55f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359151"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Просмотр операций развертывания с помощью Azure Resource Manager

Вы можете просматривать операции развертывания на портале Azure. Чаще всего необходимость просмотреть операции возникает, если во время развертывания произошла ошибка. Таким образом, эта статья посвящена просмотру операций, которые завершились с ошибкой. Портал Azure предоставляет интерфейс, который позволяет легко находить ошибки и определять возможные действия по их устранению.

Вы можете устранять неполадки развернутой службы, просматривая журналы аудита или операции развертывания. В этой статье описаны оба метода. Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Microsoft Azure
Для просмотра операций развертывания выполните следующие действия:

1. Для группы ресурсов, участвующих в развертывании, обратите внимание на состояние последнего развертывания. Можно выбрать этот статус, чтобы получить дополнительные сведения.
   
    ![состояние развертывания](./media/resource-manager-deployment-operations/deployment-status.png)
2. Вы увидите историю последних развертываний. Выберите развертывание, которое завершилось неудачно.
   
    ![состояние развертывания](./media/resource-manager-deployment-operations/select-deployment.png)
3. Щелкните ссылку, чтобы просмотреть сведения о причине сбоя развертывания. На рисунке ниже видно, что сбой произошел, потому что DNS-запись не является уникальной.  
   
    ![просмотр неудачного развертывания](./media/resource-manager-deployment-operations/view-error.png)
   
    Описания в этом сообщении об ошибке должно быть достаточно, чтобы приступить к устранению неполадки. Однако если вы хотите узнать, какие задачи выполнены, вы можете просмотреть операции, как описано ниже.
4. Вы можете просмотреть все операции развертывания. Чтобы просмотреть сведения о конкретной операции, выберите ее.
   
    ![просмотр операций](./media/resource-manager-deployment-operations/view-operations.png)
   
    На рисунке выше видно, что созданы учетная запись хранения, виртуальная сеть и группа доступности. Операция создания общедоступного IP-адреса завершилась сбоем, а попытки создать другие ресурсы не были предприняты.
5. Чтобы просмотреть события развертывания, щелкните **События**.
   
    ![просмотр событий](./media/resource-manager-deployment-operations/view-events.png)
6. В открывшемся окне отобразятся все события развертывания. Чтобы просмотреть дополнительные сведения о конкретном событии, выберите его. Обратите внимание на идентификаторы корреляции. Это значение может быть полезным при работе с технической поддержкой для устранения проблемы развертывания.
   
    ![просмотр событий](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Общее состояние развернутой службы можно получить с помощью команды **Get-AzureRmResourceGroupDeployment** . 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Вы можете отфильтровать результаты, чтобы отобразить только те развертывания, которые завершились сбоем.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Каждое развертывание включает в себя несколько операций. Каждая операция представляет шаг в процессе развертывания. Чтобы определить, что пошло не так при развертывании, обычно требуется просмотреть сведения об операциях развертывания. Состояние операций можно просмотреть с помощью команды **Get AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Эта команда возвращает несколько операций, каждая из которых представлена в следующем формате:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Чтобы получить дополнительные сведения о завершившихся сбоем операциях, получите свойства для операций с состоянием **Failed** .

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    В результате будут возвращены все завершившиеся сбоем операции. Каждая из них будет представлена в следующем формате:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Обратите внимание на значения serviceRequestId и trackingId операции. serviceRequestId может быть полезным при работе с технической поддержкой для устранения проблемы развертывания. trackingId понадобится вам на следующем шаге, чтобы рассмотреть конкретную операцию.
4. Чтобы получить сообщение о состоянии конкретной завершившейся сбоем операции, используйте следующую команду:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Возвращаемые данные:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Каждая операция развертывания в Azure включает в себя содержимое запроса и ответа. Содержимое запроса — это данные, отправляемые в Azure во время развертывания (например, создание виртуальной машины, диск операционной системы и другие ресурсы). Содержимое ответа — это данные, отправляемые Azure из запроса на развертывание. Во время развертывания можно воспользоваться параметром **DeploymentDebugLogLevel**, чтобы указать необходимость сохранения запроса или ответа в журнале. 

  Получить эту информацию из журнала и сохранить ее локально можно с помощью следующих команд PowerShell:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

1. Общее состояние развернутой службы можно получить с помощью команды **azure group deployment show** .

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. Одно из возвращаемых значений — **correlationId**. Это значение используется для отслеживания связанных событий и может быть полезно при взаимодействии со службой технической поддержки для устранения проблемы развертывания.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. Чтобы просмотреть операции развертывания, используйте следующую команду:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Получите сведения о развертывании с помощью операции [получения сведений о развертывании шаблона](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    В ответе обратите особое внимание на элементы **provisioningState**, **correlationId** и **error**. **correlationId** используется для отслеживания связанных событий и может быть полезен при взаимодействии со службой технической поддержки для устранения проблемы развертывания.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. См. дополнительные сведения о [перечислении всех операций развертывания шаблона](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    Ответ будет содержать сведения о запросе и (или) ответе, в зависимости от того, что было указано в свойстве **debugSetting** во время развертывания.

  ```json
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
  ```


## <a name="next-steps"></a>Дополнительная информация
* Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Дополнительные сведения об использовании журналов действий для мониторинга других типов действий см. в статье [Операции аудита с помощью диспетчера ресурсов](resource-group-audit.md).
* Чтобы проверить развернутую службу перед ее выполнением, ознакомьтесь со статьей [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).

