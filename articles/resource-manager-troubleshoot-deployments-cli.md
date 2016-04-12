<properties
   pageTitle="Устранение неполадок развернутых служб с помощью Azure CLI | Microsoft Azure"
   description="Описывается использование Azure CLI для обнаружения и устранения проблем развертывания Resource Manager."
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

# Устранение неполадок развертываний групп ресурсов с помощью Azure CLI

> [AZURE.SELECTOR]
- [Портал](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Интерфейс командной строки Azure](resource-manager-troubleshoot-deployments-cli.md)
- [ИНТЕРФЕЙС REST API](resource-manager-troubleshoot-deployments-rest.md)

Если вы получили ошибку при развертывании ресурсов в Azure, необходимо узнать, что пошло не так. Azure CLI предоставляет команды, которые позволяют находить ошибки и определять возможные действия по их исправлению.

Вы можете устранять неполадки развернутой службы, просматривая журналы аудита или операции развертывания. В этом разделе показаны оба способа.

Некоторых ошибок можно избежать, проверив шаблон и инфраструктуру перед развертыванием. Дополнительные сведения см. в статье [Развертывание группы ресурсов с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

## Использование журналов аудита для устранения неполадок

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Чтобы просмотреть ошибки развернутой службы, выполните следующее.

1. Чтобы просмотреть журналы аудита, выполните команду **azure group log show**. Для получения журнала только для последнего развертывания можно указать параметр **--last-deployment**.

        azure group log show ExampleGroup --last-deployment

2. Команда **azure group log show** может возвращать большой объем данных. Для устранения неполадок обычно необходимо сосредоточиться на операции, выполнить которую не удалось. Следующий сценарий использует параметр **--json** и служебную программу JSON [jq](https://stedolan.github.io/jq/) для поиска в журнале ошибок развертывания.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
            mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
            "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
        },
        ...

    Вы увидите, что в разделе **Свойства** отражаются сведения json о неудачной операции.

    Для получения более подробных сведений из журналов можно использовать параметры **--verbose** и **-vv**. Используйте параметр **--verbose**, чтобы отобразить шаги, которые проходят операции на `stdout`. Для получения полной истории запроса используйте параметр **-vv**. В сообщениях часто содержатся важные сведения о причинах ошибок.

3. Чтобы сосредоточиться на сообщении о состоянии неудачных записей, используйте следующую команду.

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## Использование операций развертывания для устранения неполадок

1. Общее состояние развернутой службы можно получить с помощью команды **azure group deployment show**. В следующем примере развертывание завершилось неудачно.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Чтобы просмотреть сообщение о завершившихся сбоем операциях развертывания, используйте следующую команду.

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## Дальнейшие действия

- Чтобы узнать об использовании журналов аудита для отслеживания других типов действий, см. раздел [Операции аудита с помощью диспетчера ресурсов](resource-group-audit.md).
- Чтобы проверить развернутую службу перед ее выполнением, см. раздел [Развертывание группы ресурсов с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->