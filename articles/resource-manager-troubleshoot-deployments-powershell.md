---
title: Просмотр операций развертывания с помощью PowerShell | Microsoft Docs
description: Описывается использование Azure PowerShell для обнаружения проблем развертывания Resource Manager.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/14/2016
ms.author: tomfitz

---
# Просмотр операций развертывания с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [Портал](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [Интерфейс командной строки Azure](resource-manager-troubleshoot-deployments-cli.md)
> * [ИНТЕРФЕЙС REST API](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

Вы можете просматривать операции развертывания с помощью Azure PowerShell. Чаще всего необходимость просмотреть операции возникает, если во время развертывания произошла ошибка. Таким образом, эта статья посвящена просмотру операций, которые завершились с ошибкой. PowerShell предоставляет командлеты, которые позволяют находить ошибки и определять возможные действия по их исправлению.

[!INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Некоторых ошибок можно избежать, проверив шаблон и инфраструктуру перед развертыванием. Вы также можете добавлять в журнал дополнительные сведения о запросах и ответах во время развертывания, которые позже могут быть полезны при устранении неполадок. Чтобы узнать о проверке и добавлении в журнал сведений о запросах и ответах, ознакомьтесь с разделом [Развертывание ресурсов с использованием шаблонов Azure Resource Manager](resource-group-template-deploy.md).

## Использование операций развертывания для устранения неполадок
1. Общее состояние развернутой службы можно получить с помощью команды **Get-AzureRmResourceGroupDeployment**. Вы можете отфильтровать результаты, чтобы отобразить только те развертывания, которые завершились сбоем.
   
        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
   
    Будет выведен список развертываний, завершившихся сбоем, в следующем формате:
   
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1
   
        Outputs                 :
        DeploymentDebugLogLevel :
2. Каждое развертывание обычно состоит из нескольких операций, каждая из которых представляет шаг процесса развертывания. Чтобы определить, что пошло не так при развертывании, обычно требуется просмотреть сведения об операциях развертывания. Состояние операций можно просмотреть с помощью команды **Get AzureRmResourceGroupDeploymentOperation**.
   
        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
   
    Эта команда возвращает несколько операций, каждая из которых представлена в следующем формате:
   
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
3. Чтобы получить дополнительные сведения о завершившихся сбоем операциях, получите свойства для операций с состоянием **Failed**.
   
        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
   
    В результате будут возвращены все завершившиеся сбоем операции. Каждая из них будет представлена в следующем формате:
   
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
   
    Запишите идентификатор отслеживания какой-либо операции. Он понадобится вам на следующем шаге, чтобы рассмотреть конкретную операцию.
4. Чтобы получить сообщение о состоянии конкретной завершившейся сбоем операции, используйте следующую команду:
   
        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
   
    Возвращаемые данные:
   
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## Использование журналов аудита для устранения неполадок
[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Чтобы просмотреть ошибки развернутой службы, выполните следующее.

1. Чтобы получить записи журнала, выполните команду **Get-AzureRmLog**. Можно использовать параметры **ResourceGroup** и **Status**, чтобы вернуть только те события, которые завершились сбоем для отдельной группы ресурсов. Если не указать время начала и окончания, возвращаются записи за последний час. Например, для получения завершившихся сбоем операций за последний час выполните следующую команду.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
   
    Можно указать определенный интервал времени. В следующем примере мы будем искать завершившиеся сбоем действия за последний день.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
   
    Или же можно задать точное время начала и завершения для поиска завершившихся сбоем действий.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed
2. Если эта команда возвращает слишком много записей и свойств, вы можете ограничить диапазон просмотра свойством **Properties**. Мы также включим параметр **DetailedOutput**, чтобы просмотреть сообщения об ошибках.
   
        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
   
    Будут выведены свойства записей журнала в следующем формате:
   
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...
3. На основании этих результатов рассмотрим второй элемент. Можно уточнить результаты, просмотрев сообщение о состоянии для этой записи.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Возвращаемые данные:
   
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## Дальнейшие действия
* Сведения об устранении некоторых ошибок развертывания см. в статье [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Дополнительные сведения об использовании журналов аудита для отслеживания других типов действий см. в разделе [Операции аудита с помощью Resource Manager](resource-group-audit.md).
* Чтобы проверить развернутую службу перед ее выполнением, см. раздел [Развертывание ресурсов с использованием шаблонов Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->