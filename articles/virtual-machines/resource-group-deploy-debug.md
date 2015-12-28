<properties
   pageTitle="Устранение неполадок при развертывании групп ресурсов| Microsoft Azure"
   description="В статье описаны распространенные проблемы развертывания ресурсов, созданных с помощью модели развертывания диспетчера ресурсов, и показано, как обнаружить и устранить эти проблемы."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="10/14/2015"
   ms.author="tomfitz;rasquill"/>

# Устранение неполадок при развертывании групп ресурсов в Azure

Когда во время развертывания возникают проблемы, нужно понять, что пошло не так. Диспетчер ресурсов предоставляет два способа, которые позволяют выяснить, что произошло и почему это произошло. С помощью команд развертывания можно получить сведения о конкретном развертывании для группы ресурсов. Также можно использовать журналы аудита для получения сведений обо всех операциях, выполняемых для группы ресурсов. С этой информацией можно устранить проблему и возобновить операции в своем решении.

Этот раздел в основном посвящен устранению неполадок развертываний с помощью команд развертывания. Сведения об использовании журналов аудита для отслеживания всех операций с ресурсами см. в разделе [Операции аудита с помощью диспетчера ресурсов](../resource-group-audit.md).

В этом разделе показано, как получить сведения об устранении неполадок с помощью Azure PowerShell, Azure CLI и API-интерфейса REST. Сведения об использовании портала предварительной версии для устранения неполадок развертывания см. в статье [Управление ресурсами Azure с помощью портала Azure](../azure-portal/resource-group-portal.md).

В этом разделе также описываются решения стандартных проблем, с которыми сталкиваются пользователи.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания. Создать группы ресурсов с классической модели развертывания невозможно.


## Устранение неполадок с помощью PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

Общее состояние развертывания можно получить с помощью команды **Get-AzureRmResourceGroupDeployment**. В следующем примере развертывание завершилось неудачно.

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

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

Каждое развертывание обычно состоит из нескольких операций, каждая из которых представляет шаг процесса развертывания. Чтобы определить, что пошло не так при развертывании, обычно требуется просмотреть сведения об операциях развертывания. Состояние операций можно просмотреть с помощью команды **Get AzureRmResourceGroupDeploymentOperation**.

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

Эта команда отображает две операции в развертывании. Одна имеет состояние подготовки "Сбой", а другая — "Завершено успешно".

Сообщение о состоянии можно получить с помощью следующей команды:

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Устранение неполадок интерфейса командной строки Azure

Общее состояние развертывания можно получить с помощью команды **azure group deployment show**. В следующем примере развертывание завершилось неудачно.

    azure group deployment show ExampleGroup ExampleDeployment

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


Дополнительные сведения о причинах сбоя развертывания можно получить в журналах аудита. Чтобы просмотреть журналы аудита, выполните команду **azure group log show**. Для получения журнала только для последнего развертывания можно указать параметр **--last-deployment**.

    azure group log show ExampleGroup --last-deployment

Команда **azure group log show** может возвращать большой объем данных. Для устранения неполадок обычно необходимо сосредоточиться на операции, выполнить которую не удалось. Следующий сценарий использует параметр **--json** и средство **jq** для поиска в журнале ошибок развертывания. Дополнительные сведения о таких средствах, как **jq**, см. в разделе [Полезные средства для работы с Azure](#useful-tools-to-interact-with-azure).

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
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

## Устранение неполадок с API REST

API-Интерфейс REST диспетчера ресурсов содержит идентификаторы URI для получения сведений о развертывании, операциях развертывания, а также сведений о конкретной операции. Полное описание этих команд см. в следующих статьях:

- [Получение сведений о развертывании шаблона](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [Список всех операций развертывания шаблона](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [Получение сведений об операции развертывании шаблона](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## Обновление истекших учетных данных

Если срок действия учетных данных Azure истек или вы не вошли в учетную запись Azure, развертывание завершится неудачно. Срок действия учетных данных может истечь, если сеанс открыт слишком долго. Обновить учетные данные можно следующими способами:

- В PowerShell используйте командлет **Login-AzureRmAccount** (или **Add-AzureAccount** для версий PowerShell, предшествующих предварительной версии 1.0). Учетных данных в файле параметров публикации недостаточно для командлетов в модуле AzureResourceManager.
- В интерфейсе командной строки Azure используйте команду **azure login**. Если возникают ошибки проверки подлинности, убедитесь, что вы [правильно настроили CLI Azure](../xplat-cli-connect.md).

## Проверка формата шаблонов и параметров

Если файл шаблона или параметра имеет неправильный формат, развертывание завершится неудачно. Перед развертыванием можно проверить правильность шаблона и параметров.

### PowerShell

В PowerShell используйте команду **Test-AzureRmResourceGroupDeployment** (или **Test-AzureResourceGroupTemplate** для версий PowerShell, предшествующих предварительной версии 1.0).

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Инфраструктура CLI Azure

В интерфейсе командной строки Azure используйте команду **azure group template validate <resource group>**

Следующий пример показывает, как проверить шаблон и все необходимые параметры. Командная строка Azure запрашивает значения требуемых параметров.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### Интерфейс REST API

Для интерфейса API REST см. [Проверка развертывания шаблона](https://msdn.microsoft.com/library/azure/dn790547.aspx).

## Проверка расположений, которые поддерживают ресурс

При указании расположения для ресурса необходимо использовать одно из расположений, которые поддерживают ресурс. Перед указанием расположения ресурса используйте одну из следующих команд, чтобы убедиться, что это расположение поддерживает тип ресурса.

### PowerShell

Для версий PowerShell, предшествующих предварительной версии 1.0, полный список ресурсов и расположений можно просмотреть с помощью команды **Get-AzureLocation**.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

Определенный тип ресурса можно указать с помощью:

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

В предварительной версии PowerShell 1.0 для получения списка поддерживаемых расположений используйте команду **Get AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Определенный тип ресурса можно указать с помощью:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Инфраструктура CLI Azure

В интерфейсе командной строки Azure можно использовать команду **azure location list**. Так как список расположений может быть длинным и включать множество поставщиков, с помощью предлагаемых средств можно изучить поставщики и расположения, прежде чем использовать расположение, которое еще не доступно. Следующий сценарий использует средство **jq** для поиска расположений, в которых доступен поставщик ресурсов для виртуальных машин Azure.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### Интерфейс REST API

Для API REST см. [Получение сведений о поставщике ресурсов](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Создание уникальных имен ресурсов

Для некоторых ресурсов, особенно учетных записей хранения, серверов баз данных и веб-сайтов, необходимо указать имя ресурса, которое будет уникальным в Azure. В настоящее время невозможно проверить, является ли имя уникальным. Мы советуем использовать схему именования, вероятность использования которой другими организациями будет низкой.

## Проблемы с проверкой подлинности, подпиской, ролями и квотами

Успешному развертыванию могут препятствовать одна или несколько из проблем, касающихся проверки подлинности, авторизации и Azure Active Directory. Независимо от того, как вы управляете группами ресурсов Azure, удостоверение, используемое для входа в учетную запись, должно быть объектом Azure Active Directory. Вы можете использовать рабочую или учебную учетную запись, которую создали или которая была вам назначена, либо создать субъект-службу для приложений.

Но Azure Active Directory позволяет вам или вашему администратору точно управлять тем, какие удостоверения могут получать доступ к тем или иным ресурсам. Если происходит сбой развертывания, проверьте запросы на наличие проблем с проверкой подлинности или авторизацией, а также изучите журналы развертывания для своей группы ресурсов. Может оказаться, что, хотя у вас есть разрешения для некоторых ресурсов, для других ресурсов их нет. Используя CLI Azure, вы можете проверить клиенты и пользователей Azure Active Directory с помощью команд `azure ad`. (Полный список команд интерфейса командной строки Azure см. в статье [Использование интерфейса командной строки Azure для Mac, Linux и Windows с диспетчером ресурсов Azure](azure-cli-arm-commands.md).)

Кроме того, могут возникнуть проблемы при достижении развертыванием квоты по умолчанию, которая может задаваться для группы ресурсов, подписок, учетных записей, а также других областей. Убедитесь, что у вас достаточно ресурсов для надлежащего развертывания. Полные сведения о квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).

Чтобы проверить квоты ядер своей подписки, воспользуйтесь командой `azure vm list-usage` в командной строке Azure и командлетом **Get-AzureVMUsage** в PowerShell. Ниже приведена команда в Azure CLI и показано, что квота ядер для бесплатно пробной учетной записи равна 4:

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Если попытаться развернуть шаблон, который создает более 4 ядер в регионе "Западная часть США" для указанной выше подписки, произойдет ошибка развертывания, которая может выглядеть следующим образом (на портале или при проверке журналов развертывания):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

В таких случаях следует перейти на портал и зарегистрировать проблему в службе поддержки, чтобы поднять свою квоту для региона, в котором требуется осуществить развертывание.

> [AZURE.NOTE]Следует помнить, что для групп ресурсов квоты устанавливаются для каждого отдельного региона, а не для всей подписки. Если необходимо развернуть 30 ядер в западной части США, необходимо запросить 30 ядер управления ресурсами в этом регионе. Если необходимо развернуть 30 ядер в любом из регионов, к которым у вас есть доступ, следует запросить 30 ядер управления ресурсами во всех регионах. <!-- --> Например, чтобы точно указать количество ядер, можно проверить регионы, для которых необходимо оценить квоту, с помощью следующей команды. Эта команда передает данные средству **jq** для анализа JSON. <!-- --> azure provider show Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }


## Проверка регистрации поставщика ресурсов

Ресурсами управляет поставщик ресурсов, и для учетной записи или подписки может быть включено использование конкретного поставщика. Если поставщик включен для использования, он также должен быть зарегистрирован. Большинство поставщиков, но не все, регистрируются автоматически порталом Azure или интерфейсом командной строки, который вы используете.

### PowerShell

Чтобы получить список поставщиков ресурсов и состояние регистрации, воспользуйтесь командлетом **Get-AzureProvider** (для версий PowerShell, предшествующих предварительной версии 1.0).

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

Чтобы зарегистрировать поставщика, воспользуйтесь командой **Register-AzureProvider**.

В предварительной версии PowerShell 1.0 используйте команду **Get AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Чтобы зарегистрировать поставщика, воспользуйтесь командой **Register-AzureRmResourceProvider**.

### Инфраструктура CLI Azure

Чтобы узнать, зарегистрирован ли поставщик для использования, выполните в CLI Azure команду `azure provider list` (приведен сокращенный пример вывода).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Опять же, если вам нужна дополнительная информация о поставщиках, включая их доступность в регионах, введите `azure provider list --json`. Следующий код выбирает только первого из них в списке для просмотра:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

Если поставщик требует регистрации, используйте команду `azure provider register <namespace>`, где значение *namespace* берется из приведенного выше списка.

### Интерфейс REST API

Для получения состояния регистрации см. [Получение сведений о поставщике ресурсов](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Для регистрации поставщика см. [Регистрация подписки с поставщиком ресурсов](https://msdn.microsoft.com/library/azure/dn790548.aspx).


## Как узнать, что развертывание пользовательских шаблонов завершилось успешно

Если вы используете шаблоны, которые создали сами, важно понимать, что система управления ресурсами Azure сообщает об успешном выполнении при успешном возврате всех поставщиков из развертывания. Это значит, что все элементы ваших шаблонов были развернуты для использования.

Тем не менее это не обязательно означает, что ваша группа ресурсов "активна и готова для пользователей". Например, для большинства развертываний требуется скачать обновления, дождаться других ресурсов, не являющихся шаблонами, или установить сложные сценарии либо другое исполняемое действие, о которым Azure не знает, так как оно не отслеживается поставщиком. В таких случаях ресурсы могут быть готовы к практическому использованию только спустя некоторое время. В результате этого следует ожидать, что состояние развертывания меняется на "успешно" за некоторое время до того, прежде чем развертывание можно будет использовать.

Тем не менее вы можете сделать так, чтобы Azure не сообщал об успешном развертывании, создав настраиваемый сценарий для пользовательского шаблона (например, с помощью [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)), который умеет отслеживать все развертывание на предмет готовности в рамках всей системы и возвращает успешный результат только в том случае, если пользователи могут работать со всем развертыванием. Если вы хотите сделать так, чтобы ваше расширение выполнялось последним, используйте свойство **dependsOn** в шаблоне. В качестве примера можно привести [создание развертываний шаблона](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Полезные средства для работы с Azure
При работе с ресурсами Azure из командной строки вы воспользуетесь набором инструментов, который поможет выполнить необходимые задачи. Шаблоны группы ресурсов Azure представляют собой документы JSON, а API диспетчера ресурсов Azure принимает и возвращает JSON, поэтому средства анализа JSON станут одними из первых инструментов, которые вы будете использовать для просмотра сведений о ресурсах и для разработки шаблонов и файлов параметров шаблонов и взаимодействия с ними.

### Средства Mac, Linux и Windows
Если вы используете интерфейс командной строки Azure для Mac, Linux или Windows, то, скорее всего, вы уже знакомы со стандартными средствами для загрузки, такими как **[curl](http://curl.haxx.se/)**, **[wget](https://www.gnu.org/software/wget/)** или **[Resty](https://github.com/beders/Resty)**, со служебными программами JSON, например **[jq](http://stedolan.github.io/jq/download/)** и **[jsawk](https://github.com/micha/jsawk)**, а также с библиотеками языков, которые хорошо обрабатывают JSON. (Многие из этих средств, например [wget](http://gnuwin32.sourceforge.net/packages/wget.htm), также портированы в Windows; фактически сделать так, чтобы программы для Linux и другие программные средства с открытым исходным кодом также работали в Windows, можно несколькими способами.)

Этот раздел содержит некоторые команды CLI Azure, которые вы можете использовать с **jq** для эффективного получения именно тех сведений, которые вам нужны. Чтобы лучше понять, как используются ресурсы Azure, выберите тот набор инструментов, к которому вы привыкли.

### PowerShell

В Windows PowerShell есть несколько основных команд для выполнения тех же процедур.

- С помощью командлета **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** скачайте такие файлы, как шаблоны группы ресурсов или JSON-файлы параметров.
- Используйте командлет **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** для преобразования строки JSON в настраиваемый объект ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)), содержащий свойство для каждого поля в строке JSON.


## Дальнейшие действия

Чтобы освоить создание шаблонов, прочтите статью [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md) и найдите развертываемые примеры в [репозитории шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates). Примеры свойства **dependsOn** представлены в статье [Создание виртуальной машины с несколькими сетевыми адаптерами, доступной через протокол RDP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=AcomDC_1217_2015-->