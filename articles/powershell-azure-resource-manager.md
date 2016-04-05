<properties 
	pageTitle="Использование Azure PowerShell с диспетчером ресурсов | Microsoft Azure" 
	description="Общие сведения об использовании Azure PowerShell для развертывания нескольких ресурсов в виде группы ресурсов в Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/17/2016" 
	ms.author="tomfitz"/>

# Использование Azure PowerShell с диспетчером ресурсов Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)

Диспетчер ресурсов Azure предлагает кардинально новый способ представления ресурсов Azure. Вместо того чтобы создавать отдельные ресурсы и управлять ими, вы для начала представляете себе полное решение, такое как блог, коллекция фотографий, портал SharePoint или вики-страница. Вы используете шаблон (декларативное представление решения), чтобы создать группу ресурсов, содержащую все ресурсы, необходимые для поддержки решения. Затем вы развертываете группу ресурсов и управляете нею как логической единицей.

В этом учебнике вы узнаете, как использовать Azure PowerShell с диспетчером ресурсов Azure. Здесь рассматривается процесс создания и развертывания группы ресурсов для размещенного в Azure веб-приложения с базой данных SQL и всеми ресурсами, которые необходимы для его поддержки.

## Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

- Учетная запись Azure.
  + Вы можете [открыть учетную запись Azure бесплатно](/pricing/free-trial/?WT.mc_id=A261C142F) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после использования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.
  
  + Вы можете [активировать преимущества подписчика MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) — ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты служб Azure.
- Azure PowerShell 1.0. Информацию об этом выпуске и его установке см. в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).

Этот учебник предназначен для начинающих пользователей PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы.

## Что именно развертывается

В этом учебнике будет использоваться Azure PowerShell для развертывания веб-приложения и базы данных SQL. Тем не менее это решение базы данных SQL и веб-приложения состоит из различных ресурсов, которые работают совместно. Фактически вы развернете следующие ресурсы:

- сервер SQL для размещения базы данных;
- база данных SQL для хранения данных;
- правила брандмауэра, чтобы разрешить веб-приложению подключаться к базе данных;
- План службы приложений для определения возможностей и стоимости веб-приложения;
- веб-сайт для запуска веб-приложения;
- Файл web.config для хранения строки подключения к базе данных. 

## Справка по командлетам

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Get-AzureRmResource, введите:

	Get-Help Get-AzureRmResource -Detailed

Чтобы получить список командлетов в модуле ресурсов вместе с краткой справочной информацией, введите:

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

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

Чтобы войти в учетную запись Azure, используйте командлет **Login-AzureRmAccount**.

    PS C:\> Login-AzureRmAccount

Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он загружает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

Срок действия параметров учетной записи истекает, поэтому необходимо их периодически обновлять. Чтобы обновить параметры учетной записи, еще раз выполните командлет **Login-AzureRmAccount**.

>[AZURE.NOTE] Для модулей диспетчера ресурсов требуется командлет Login-AzureRmAccount. Файла параметров публикации недостаточно.

## Получение расположений типов ресурсов

Во время развертывания ресурсов укажите, где следует разместить ресурс. Не каждый регион поддерживает все типы ресурсов. Прежде чем развертывать веб-приложение и базу данных SQL, выясните, какие регионы поддерживают эти типы ресурсов. Группа ресурсов может содержать ресурсы, которые находятся в разных регионах. Однако, если это возможно, создавайте ресурсы в том же расположении, чтобы оптимизировать производительность. В частности, необходимо убедиться в том, что база данных находится в том же расположении, что и обращающееся к ней приложение.

Чтобы получить расположения, поддерживающие каждый тип ресурса, используйте командлет **Get-AzureRmResourceProvider**. Посмотрим, какой результат возвращает эта команда:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

ProviderNamespace представляет коллекцию связанных типов ресурсов. Эти пространства имен обычно соответствуют службам, которые нужно создать в Azure. Если вы хотите использовать поставщик ресурсов, указанный как **незарегистрированный**, зарегистрируйте этот поставщик ресурсов. Для этого выполните командлет **Register-AzureRmResourceProvider** и укажите пространство имен поставщика для регистрации. Скорее всего поставщик ресурсов, который вы будете использовать в этом учебнике, уже зарегистрирован для вашей подписки.

Чтобы получить дополнительные сведения о поставщике, укажите пространство имен:

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Чтобы ограничить вывод в поддерживаемые расположения для определенного типа ресурсов, например веб-сайтов, используйте следующую команду:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Результат должен быть аналогичен приведенному ниже:

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
    Central US
    East US 2

Расположения, которые вы видите, могут немного отличаться от предыдущих результатов. Результаты могут отличаться, так как администратор организации создал политику, которая ограничивает регионы, доступные для использования в вашей подписке. Или ограничения связаны с налоговой политикой вашей страны.

Выполним ту же команду для базы данных:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

Похоже, эти ресурсы доступны во многих регионах. В этом разделе мы будем использовать **Запад США**, но вы можете указать любой поддерживаемый регион.

## Создание группы ресурсов

В этом разделе учебника рассматривается процесс создания группы ресурсов. Группа ресурсов будет служить контейнером для всех ресурсов в решении, имеющих одинаковый жизненный цикл. Позже, следуя инструкциям этого учебника, вы развернете в эту группу ресурсов веб-приложение и базу данных SQL.

Чтобы создать группу ресурсов, используйте командлет **New-AzureRmResourceGroup**.

В команде используются параметр **Name** для указания имени группы ресурсов и параметр **Location** для указания ее расположения. С учетом информации из предыдущего раздела мы будем использовать в качестве расположения "Запад США".

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Группа ресурсов успешно создана.


## Получение доступных версий API для ресурсов

При развертывании шаблона нужно указать версию API, используемую для создания ресурса. Доступная версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. Поставщики ресурсов активируют новые функции, они выпускают новые версии API-интерфейса REST. Следовательно, версия API, которую вы указываете в шаблоне, влияет на свойства, доступные после создания шаблона. Как правило, во время создания шаблонов следует выбирать последнюю версию API. Для существующих шаблонов можно продолжить использовать версию API, которая не изменит развертывание, или обновить шаблон до последней версии, чтобы воспользоваться новыми возможностями.

Этот шаг может показаться противоречивым, но вам не будет сложно найти версии API, доступные для вашего ресурса. Для этого используйте еще раз командлет **Get-AzureRmResourceProvider**.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Как вы видите, этот API часто обновляется. Как правило, те же номера версии API доступны для всех ресурсов в поставщике ресурсов. Исключение будет сделано, если ресурс добавлен или удален в определенный момент. Мы предполагаем, что те же версии API доступны для ресурса serverFarms. Но вы можете перепроверить версии для ресурса, который, по вашему мнению, может иметь другой список доступных версий API.

Для базы данных вы увидите такие версии:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## Создание шаблона

В этом разделе не объясняется, как создать шаблон, и не рассматривается структура шаблона. Эту информацию см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md). Ниже приведен шаблон, который будет развернут. Обратите внимание, что шаблон использует версии API, полученные в предыдущем разделе. Чтобы все ресурсы находились в одном регионе, мы применим выражение шаблона **resourceGroup().location**, чтобы использовать расположение группы ресурсов.

Также обратите внимание на раздел параметров. Этот раздел определяет значения, которые можно задать во время развертывания ресурсов. Эти значения понадобятся вам позже.

Вы можете скопировать шаблон и сохранить его как JSON-файл. В этом учебнике предполагается, что он был сохранен по адресу c:\\Azure\\Templates\\azuredeploy.json, но вы можете сохранить в любом удобном для вас месте и под любым именем.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## Развертывание шаблона

У вас есть группа ресурсов и шаблон, поэтому вы можете развернуть инфраструктуру, определенную в шаблоне, в группу ресурсов. Разверните ресурсы, используя командлет **New-AzureRmResourceGroupDeployment**. Базовый синтаксис выглядит вот так:

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

Укажите группу ресурсов и расположение шаблона. Если шаблон не является локальным, можно использовать параметр **-TemplateUri** и указать универсальный код ресурса (URI) для шаблона. Для параметра **-Mode** можно задать значение **Добавочный** или **Полный**. По умолчанию диспетчер ресурсов выполняет добавочное обновление во время развертывания. Таким образом, не обязательно задавать параметр **-Mode**, когда требуется **Добавочный**. Сведения об отличиях этих режимов развертывания см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

###Динамические параметры шаблона

Если вы знакомы с PowerShell, вы знаете,что вы можете проходить по кругу все доступные параметры для командлета. Для этого введите знак минус (-) и нажмите клавишу TAB. Точно так же это работает с параметрами, которые вы определили в шаблоне. Сразу после ввода имени шаблона командлет выбирает шаблон, анализирует его и динамически добавляет параметры шаблона в команду. Таким образом можно легко указать значения параметров шаблона. Если вы забыли значение обязательного параметра, PowerShell подскажет его вам.

Ниже показана полная команда с включенными параметрами. Вы можете указать собственные значения для имен ресурсов.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

При вводе команды запрашивается отсутствующий обязательный параметр **administratorLoginPassword**. А после ввода пароля значение защищенной строки скрывается. Эта стратегия исключает риск, сопряженный с вводом пароля в виде обычного текста.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Если шаблон включает параметр с именем, которое совпадает с одним из параметров в команде для развертывания шаблона (например включение в шаблон параметра с именем **ResourceGroupName**, которое совпадает с именем параметра **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), вам будет предложено указать название для параметра с постфиксом **FromTemplate** (например **ResourceGroupNameFromTemplate**). В целом следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

Команда выполняется и возвращает сообщения по мере создания ресурсов. Наконец, вы можете увидеть результат развертывания.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

Всего за несколько шагов мы создали и развернули ресурсы, необходимые для сложного веб-сайта.

## Получение сведений о ваших группах ресурсов

После создания группы ресурсов, вы можете использовать командлеты в модуле диспетчера ресурсов для управления ими.

- Чтобы получить все группы ресурсов, входящие в вашу подписку, используйте командлет **Get-AzureRmResourceGroup**:

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      Если необходимо получить конкретную группу ресурсов, укажите параметр **Name**.
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- Чтобы получить ресурсы, входящие в группу ресурсов, используйте командлет **Find-AzureRmResource** с параметром **ResourceGroupNameContains**. Без параметров командлет Find-AzureRmResource возвращает все ресурсы в подписке Azure.

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- Приведенный выше шаблон содержит тег для одного ресурса. Теги можно использовать для логической организации всех ресурсов в вашей подписке. Воспользуйтесь командами **Find-AzureRmResource** и **Find-AzureRmResourceGroup** для запрашивания ресурсов по тегам.

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      С тегами можно выполнять и массу других действий. Дополнительные сведения см. в статье [Использование тегов для организации ресурсов в Azure](resource-group-using-tags.md).

## Добавление ресурсов в группу

Чтобы добавить ресурс в группу ресурсов, используйте командлет **New-AzureRmResource**. Однако добавление ресурса таким образом может привести к путанице в будущем, потому что новый ресурс не существует в шаблоне. Если вы повторно развернете старый шаблон, будет развернуто неполное решение. Если вы часто выполняете развертывание, будет проще и надежнее добавить новый ресурс в шаблон и повторно развернуть его.

## Перемещение ресурса

Существующие ресурсы можно переместить в новую группу ресурсов. Примеры см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## Удаление группы ресурсов

- Чтобы удалить ресурс из группы ресурсов, используйте командлет **Remove-AzureRmResource**. Этот командлет удаляет ресурс, но не удаляет группу ресурсов.

	Эта команда удаляет веб-сайт TestSite из группы ресурсов TestRG1.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Чтобы удалить группу ресурсов, используйте командлет **Remove-AzureRmResourceGroup**. Этот командлет удаляет группу ресурсов и входящие в нее ресурсы.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## Дальнейшие действия

- Сведения о создании шаблонов диспетчера ресурсов см. в статье [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md).
- Инструкции по развертыванию шаблонов см. в статье [Развертывание приложения с помощью шаблона диспетчера ресурсов Azure](./resource-group-template-deploy.md).
- Подробный пример развертывания проекта см. в статье [Предсказуемое развертывание микрослужб в Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Сведения об устранении неполадок развертывания, которое завершилось сбоем, см. в статье [Устранение неполадок развертывания группы ресурсов в Azure](./resource-manager-troubleshoot-deployments-powershell.md).

<!---HONumber=AcomDC_0330_2016-->