<properties
   pageTitle="Устранение неполадок развернутых служб с помощью PowerShell | Microsoft Azure"
   description="Описывается использование PowerShell для обнаружения и устранения проблем развертывания Resource Manager."
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

# Устранение неполадок развертываний групп ресурсов с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [Портал](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Интерфейс командной строки Azure](resource-manager-troubleshoot-deployments-cli.md)
- [ИНТЕРФЕЙС REST API](resource-manager-troubleshoot-deployments-rest.md)

Если вы получили ошибку при развертывании ресурсов в Azure, необходимо узнать, что пошло не так. Azure PowerShell предоставляет командлеты, которые позволяют находить ошибки и определять возможные действия по их исправлению.

Вы можете устранять неполадки развернутой службы, просматривая журналы аудита или операции развертывания. В этом разделе показаны оба способа.

Некоторых ошибок можно избежать, проверив шаблон и инфраструктуру перед развертыванием. Дополнительные сведения см. в статье [Развертывание группы ресурсов с использованием шаблона Azure Resource Manager](resource-group-template-deploy.md).

## Использование журналов аудита для устранения неполадок

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Чтобы просмотреть ошибки развернутой службы, выполните следующее.

1. Чтобы получить записи журнала, выполните команду **Get-AzureRmLog**. Можно использовать параметры **ResourceGroup** и **Status**, чтобы вернуть только те события, которые завершились сбоем для отдельной группы ресурсов. Если не указать время начала и окончания, возвращаются записи за последний час. Например, для получения завершившихся сбоем операций за последний час выполните следующую команду.

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Можно указать определенный интервал времени. В следующем примере мы будем искать завершившиеся сбоем действия за последние 14 дней.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    Или же можно задать точное время начала и завершения для поиска завершившихся сбоем действий.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Если эта команда возвращает слишком много записей и свойств, вы можете ограничить диапазон просмотра свойством **Properties**. Мы также включим параметр **DetailedOutput**, чтобы просмотреть сообщения об ошибках.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    Будут выведены свойства записей журнала в следующем формате:
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. Можно уточнить результаты, просмотрев сообщение о состоянии для конкретной записи.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    Будет выведено сообщение о состоянии в следующем формате:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## Использование операций развертывания для устранения неполадок

1. Общее состояние развернутой службы можно получить с помощью команды **Get-AzureRmResourceGroupDeployment**. Вы можете отфильтровать результаты, чтобы отобразить только те развертывания, которые завершились сбоем.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Будет выведен список развертываний, завершившихся сбоем, в следующем формате:
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. Каждое развертывание обычно состоит из нескольких операций, каждая из которых представляет шаг процесса развертывания. Чтобы определить, что пошло не так при развертывании, обычно требуется просмотреть сведения об операциях развертывания. Состояние операций можно просмотреть с помощью команды **Get AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
    Будут выведены операции в следующем формате:
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. Чтобы получить дополнительные сведения об операции, необходимо извлечь объект **Properties**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    Будут выведены свойства операции в следующем формате:
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. Чтобы сосредоточиться на сообщении о состоянии завершившихся сбоем операций, используйте следующую команду.

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    Будет выведено сообщение о состоянии в следующем формате:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## Дальнейшие действия

- Дополнительные сведения об использовании журналов аудита для отслеживания других типов действий см. в разделе [Операции аудита с помощью Resource Manager](resource-group-audit.md).
- Чтобы проверить развернутую службу перед ее выполнением, см. раздел [Развертывание группы ресурсов с использованием шаблона Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0420_2016-->