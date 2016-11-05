---
title: Использование Azure PowerShell с диспетчером ресурсов | Microsoft Docs
description: Общие сведения об использовании Azure PowerShell для развертывания нескольких ресурсов в виде группы ресурсов в Azure.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: tomfitz

---
# Использование Azure PowerShell с диспетчером ресурсов Azure
> [!div class="op_single_selector"]
> * [Портал](azure-portal/resource-group-portal.md)
> * [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [ИНТЕРФЕЙС REST API](resource-manager-rest-api.md)
> 
> 

Azure Resource Manager реализует современный подход к управлению жизненным циклом ресурсов Azure. Вместо того чтобы создавать отдельные ресурсы и управлять ими, вы для начала представляете себе полное решение, такое как блог, коллекция фотографий, портал SharePoint или вики-страница. Вы используете шаблон (декларативное представление решения), чтобы определить группу ресурсов, содержащую все ресурсы, необходимые для поддержки решения. Затем вы развертываете группу ресурсов и управляете ею как логической единицей.

В этом учебнике вы узнаете, как использовать Azure PowerShell с диспетчером ресурсов Azure. Здесь рассматривается процесс развертывания и использования решения. Мы будем использовать Azure PowerShell и шаблон Resource Manager, чтобы развернуть:

* сервер SQL для размещения базы данных;
* база данных SQL для хранения данных;
* правила брандмауэра, чтобы разрешить веб-приложению подключаться к базе данных;
* План службы приложений для определения возможностей и стоимости веб-приложения;
* веб-сайт для запуска веб-приложения;
* Файл web.config для хранения строки подключения к базе данных.
* правила создания оповещений для мониторинга производительности и отслеживания ошибок;
* App Insights для настройки автоматического масштабирования.

## Предварительные требования
Для работы с этим учебником необходимы указанные ниже компоненты.

* Учетная запись Azure.
  
  * Вы можете [открыть учетную запись Azure бесплатно](/pricing/free-trial/?WT.mc_id=A261C142F) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после использования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.
  * Вы можете [активировать преимущества подписчика MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) — ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты служб Azure.
* Azure PowerShell 1.0. Сведения об этом выпуске и его установке см. в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).

Этот учебник предназначен для начинающих пользователей PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы.

## Справка по командлетам
Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

    Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Get-AzureRmResource, введите:

    Get-Help Get-AzureRmResource -Detailed

Чтобы получить список командлетов в модуле ресурсов вместе с краткой справочной информацией, введите:

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

Выходные данные будут выглядеть примерно следующим образом:

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Чтобы получить полную справку для командлета, введите команду в формате:

    Get-Help <cmdlet-name> -Full

## Вход в учетную запись Azure.
Прежде чем начать работу над решением, необходимо войти в учетную запись.

Чтобы войти в учетную запись Azure, используйте командлет **Add-AzureRmAccount**.

    Add-AzureRmAccount

Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он загружает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

Срок действия параметров учетной записи истекает, поэтому необходимо их периодически обновлять. Чтобы обновить параметры учетной записи, еще раз выполните командлет **Add-AzureRmAccount**.

> [!NOTE]
> Для модулей Resource Manager требуется командлет Add-AzureRmAccount. Файла параметров публикации недостаточно.
> 
> 

Если у вас несколько подписок, укажите идентификатор той, которая будет использоваться для развертывания, с помощью командлета **Set-AzureRmContext**.

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## Создание группы ресурсов
Прежде чем развертывать ресурсы в подписке, необходимо создать группу ресурсов, которая будет их содержать.

Чтобы создать группу ресурсов, используйте командлет **New-AzureRmResourceGroup**.

В команде используются параметр **Name** для указания имени группы ресурсов и параметр **Location** для указания ее расположения. С учетом информации из предыдущего раздела мы будем использовать в качестве расположения "Запад США".

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"

Результат должен быть аналогичен приведенному ниже:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Группа ресурсов успешно создана.

## Развертывание решения
В этой статье не объясняется, как создать шаблон, и не рассматривается структура шаблона. Эти сведения см. в статьях [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md) и [Пошаговое руководство по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md). Вы развернете предопределенный шаблон [веб-приложения, подготовленного к работе с базой данных SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/), который приведен на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

У вас есть группа ресурсов и шаблон, поэтому вы можете развернуть инфраструктуру, определенную в шаблоне, в группу ресурсов. Разверните ресурсы, используя командлет **New-AzureRmResourceGroupDeployment**. Шаблон указывает множество значений по умолчанию, которые мы и будем использовать. Таким образом, вам не придется задавать значения для этих параметров. Базовый синтаксис выглядит вот так:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Укажите группу ресурсов и расположение шаблона. Если шаблон является локальным файлом, используйте параметр **-TemplateFile** и укажите путь к шаблону. Для параметра **-Mode** можно задать значение **Добавочный** или **Полный**. По умолчанию диспетчер ресурсов выполняет добавочное обновление во время развертывания. Таким образом, не обязательно задавать параметр **-Mode**, когда требуется **Добавочный**. Сведения об отличиях этих режимов развертывания см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

### Динамические параметры шаблона
Если вы знакомы с PowerShell, вы знаете,что вы можете проходить по кругу все доступные параметры для командлета. Для этого введите знак минус (-) и нажмите клавишу TAB. Точно так же это работает с параметрами, которые вы определили в шаблоне. Сразу после ввода имени шаблона командлет выбирает шаблон, анализирует его и динамически добавляет параметры шаблона в команду. Таким образом можно легко указать значения параметров шаблона.

При вводе команды запрашивается отсутствующий обязательный параметр **administratorLoginPassword**. А после ввода пароля значение защищенной строки скрывается. Эта стратегия исключает риск, сопряженный с вводом пароля в виде обычного текста.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Шаблон может содержать параметр с именем, которое совпадает с одним из параметров в команде для развертывания шаблона (например, в шаблоне есть параметр с именем **ResourceGroupName**, которое совпадает с именем параметра **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)). В таком случае вам будет предложено указать значение для параметра с постфиксом **FromTemplate** (например, **ResourceGroupNameFromTemplate**). В целом следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

Команда выполняется и возвращает сообщения по мере создания ресурсов. Наконец, вы можете увидеть результат развертывания.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net

    DeploymentDebugLogLevel :

Всего за несколько шагов мы создали и развернули ресурсы, необходимые для сложного веб-сайта.

### Ведение журнала отладочной информации
При развертывании шаблона можно ввести журнал дополнительных сведений о запросах и ответах. Для этого во время выполнения команды **New-AzureRmResourceGroupDeployment** необходимо указать параметр **-DeploymentDebugLogLevel**. Эта информация может помочь устранить ошибки развертывания. По умолчанию задано значение **None**. В этом случае содержимое запросов и ответов не регистрируется. Можно задать ведение журнала содержимого запроса, ответа или и того, и другого. Дополнительные сведения об устранении неполадок развертывания и ведении журнала отладочной информации см. в статье [Просмотр операций развертывания с помощью Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). В следующем примере для развертывания задан параметр ведения журнала содержимого запроса и ответа.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [!NOTE]
> При указании параметра DeploymentDebugLogLevel внимательно рассмотрите тип данных, передаваемых в ходе развертывания. При ведении журнала с информацией о запросе или ответе возможно раскрытие конфиденциальных данных, извлекаемых с помощью операций развертывания.
> 
> 

## Получение сведений о ваших группах ресурсов
После создания группы ресурсов, вы можете использовать командлеты в модуле диспетчера ресурсов для управления ими.

* Чтобы получить группу ресурсов своей подписки, используйте командлет **Get-AzureRmResourceGroup**.
  
        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
  
    Он возвратит следующую информацию:
  
        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
  
        ...
  
    Если не указать имя группы ресурсов, командлет возвратит все группы ресурсов в подписке.
* Чтобы получить ресурсы, входящие в группу ресурсов, используйте командлет **Find-AzureRmResource** с параметром **ResourceGroupNameContains**. Без параметров командлет Find-AzureRmResource возвращает все ресурсы в подписке Azure.
  
        Find-AzureRmResource -ResourceGroupNameContains TestRG1
  
     Он возвращает список ресурсов в следующем виде:
  
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
* Вы можете логически упорядочить ресурсы в подписке с использованием тегов и извлечь ресурсы с помощью командлетов **Find-AzureRmResource** и **Find-AzureRmResourceGroup**.
  
        Find-AzureRmResource -TagName displayName -TagValue Website
  
        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
  
      С тегами можно выполнять и массу других действий. Дополнительные сведения см. в статье [Использование тегов для организации ресурсов в Azure](resource-group-using-tags.md).

## Добавление ресурсов в группу
Чтобы добавить ресурс в группу ресурсов, используйте командлет **New-AzureRmResource**. Однако добавление ресурса таким образом может привести к путанице в будущем, потому что новый ресурс не существует в шаблоне. Если вы повторно развернете старый шаблон, будет развернуто неполное решение. Если вы часто выполняете развертывание, будет проще и надежнее добавить новый ресурс в шаблон и повторно развернуть его.

## Перемещение ресурса
Существующие ресурсы можно переместить в новую группу ресурсов. Примеры см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## Экспорт шаблона
Для существующей группы ресурсов (развернутой с помощью PowerShell или других средств, например через портал) можно просмотреть шаблон Resource Manager. Экспорт шаблона обеспечивает два преимущества:

1. Вы можете с легкостью автоматизировать будущие развертывания решения, так как в шаблоне определены все компоненты инфраструктуры.
2. Вы можете ознакомиться с синтаксисом шаблона, просмотрев представление JSON решения.

С помощью PowerShell можно создать шаблон, который представляет текущее состояние вашей группы ресурсов, или извлечь шаблон, который использовался для конкретного развертывания.

Экспортировать шаблон для группы ресурсов целесообразно, если в нее внесены изменения и вам нужно получить представление JSON текущего состояния. Однако созданный шаблон содержит только минимальное число параметров, и в нем не указаны какие-либо переменные. Большая часть значений в шаблоне заданы жестко. Прежде чем развертывать созданный шаблон, можно преобразовать дополнительные значения в параметры, чтобы настроить развертывание для разных сред.

Экспортировать шаблон для конкретного развертывания целесообразно, если необходимо просмотреть фактический шаблон, который использовался для развертывания ресурсов. Этот шаблон будет содержать все параметры и переменные, определенные для исходного развертывания. Тем не менее если кто-то в организации внес изменения в группу ресурсов, в частности в параметры вне шаблона, этот шаблон не будет представлять текущее состояние группы ресурсов.

> [!NOTE]
> Сейчас доступна только предварительная версия функции экспорта шаблона, которая поддерживается не для всех типов ресурсов. При попытке экспорта шаблона может появиться сообщение о том, что некоторые ресурсы не экспортированы. При необходимости эти ресурсы можно определить вручную после скачивания шаблона.
> 
> 

### Экспорт шаблона из группы ресурсов
Чтобы просмотреть шаблон для группы ресурсов, выполните командлет **Export-AzureRmResourceGroup**.

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json

### Скачивание шаблона из развертывания
Чтобы скачать шаблон, используемый для конкретного развертывания, выполните командлет **Save-AzureRmResourceGroupDeploymentTemplate**.

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## Удаление ресурсов или группы ресурсов
* Чтобы удалить ресурс из группы ресурсов, используйте командлет **Remove-AzureRmResource**. Этот командлет удаляет ресурс, но не удаляет группу ресурсов.
  
    Эта команда удаляет веб-сайт TestSite из группы ресурсов TestRG1.
  
        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01
* Чтобы удалить группу ресурсов, используйте командлет **Remove-AzureRmResourceGroup**. Этот командлет удаляет группу ресурсов и входящие в нее ресурсы.
  
        Remove-AzureRmResourceGroup -Name TestRG1
  
    Вам будет предложено подтвердить удаление.
  
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## Скрипт развертывания
В предыдущих примерах развертывания в этой статье приведены только отдельные командлеты, необходимые для развертывания ресурсов в Azure. В следующем примере показан скрипт развертывания, который создает группу ресурсов, а затем развертывает ресурсы.

    <#
      .SYNOPSIS
      Deploys a template to Azure

      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## Дальнейшие действия
* Сведения о создании шаблонов диспетчера ресурсов см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Инструкции по развертыванию шаблонов см. в статье [Развертывание приложения с помощью шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).
* Подробный пример развертывания проекта см. в статье [Предсказуемое развертывание микрослужб в Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
* Сведения об устранении неполадок развертывания, которое завершилось сбоем, см. в статье [Устранение неполадок развертывания группы ресурсов в Azure](resource-manager-troubleshoot-deployments-powershell.md).

<!---HONumber=AcomDC_0914_2016-->