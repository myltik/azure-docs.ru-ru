<properties 
	pageTitle="Использование Azure PowerShell с диспетчером ресурсов Azure" 
	description="Используйте Azure PowerShell для развертывания нескольких ресурсов в виде группы ресурсов в Azure." 
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
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Использование Azure PowerShell с диспетчером ресурсов Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Диспетчер ресурсов Azure предлагает кардинально новый способ представления ресурсов Azure. Вместо того чтобы создавать отдельные ресурсы и управлять ими, вы для начала представляете себе сложную службу, такую как блог, коллекция фотографий, портал SharePoint или вики-страница. Вы используете в качестве шаблона модель ресурса, предоставляемую службой, чтобы создать группу ресурсов, в которую входят ресурсы, необходимые для поддержки службы. Затем при развертывании и управлении можно использовать группу ресурсов как логическую единицу.

В этом учебнике вы узнаете, как использовать Azure PowerShell с диспетчером ресурсов Azure для Microsoft Azure. Здесь рассматривается процесс создания и развертывания группы ресурсов для размещенного в Azure веб-приложения с базой данных SQL и всеми ресурсами, которые необходимы для его поддержки.

## Предварительные требования

Для работы с этим учебником необходима программа Azure PowerShell версии 0.8.0 или более поздней. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Как установить и настроить Azure PowerShell](powershell-install-configure.md);

Этот учебник предназначен для начинающих пользователей PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell см. в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

	Get-Help Add-AzureAccount -Detailed

## О модулях Azure PowerShell
Начиная с версии 0.8.0, в установку Azure PowerShell включено несколько модулей PowerShell. Необходимо явно указать, следует ли использовать команды из модуля Azure или из модуля диспетчера ресурсов Azure. Чтобы упростить переключение между ними, мы добавили новый командлет **Switch-AzureMode** в модуль профиля Azure.

При использовании Azure PowerShell командлеты в модуле Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет Switch-AzureMode. Он удаляет модуль Azure из сеанса и импортирует модули диспетчера ресурсов и профиля Azure.

Чтобы перейти в модуль AzureResoureManager, введите:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Чтобы вернуться в модуль Azure, введите:

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

По умолчанию действие командлета Switch-AzureMode распространяется только на текущий сеанс. Для эффективного переключения во всех сеансах PowerShell используйте параметр **Global** командлета Switch-AzureMode.

Чтобы получить справку по командлету Switch-AzureMode, введите `Get-Help Switch-AzureMode` или см. раздел [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
Чтобы получить список командлетов в модуле AzureResourceManager вместе с краткой справочной информацией, введите:

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

Выходные данные будут выглядеть примерно следующим образом:

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

Чтобы получить полную справку для командлета, введите команду в формате:

	Get-Help <cmdlet-name> -Full

Например,

	Get-Help Get-AzureLocation -Full

Полный набор команд диспетчера ресурсов Azure см. в разделе [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765).
  
## Создание группы ресурсов

В этом разделе учебника рассматривается процесс создания и развертывания группы ресурсов для веб-приложения с базой данных SQL.

Для выполнения этой задачи не нужно быть экспертом по Azure, SQL, веб-приложениям или управлению ресурсами. Шаблоны предоставляют модель группы ресурсов со всеми ресурсами, которые вам могут понадобиться. А поскольку мы используем Windows PowerShell для автоматизации задач, вы можете рассматривать этот процесс как модель для создания сценариев более масштабных задач.

### Шаг 1. Переход в диспетчер ресурсов Azure 
1. Запустите PowerShell. Вы можете использовать любую основную программу, например консоль PowerShell или интегрированную среду сценариев Windows PowerShell.

2. Используйте командлет **Switch-AzureMode** для импорта командлетов в модулях AzureResourceManager и AzureProfile.

        PS C:\> Switch-AzureMode AzureResourceManager

3. Чтобы добавить учетную запись Azure в сеанс Windows PowerShell, используйте командлет **Add-AzureAccount**.

        PS C:\> Add-AzureAccount

Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он скачивает параметры учетной записи, чтобы они были доступны в Windows PowerShell.

Срок действия параметров учетной записи истекает, поэтому необходимо их периодически обновлять. Чтобы обновить параметры учетной записи, еще раз выполните командлет **Add-AzureAccount**.

>[AZURE.NOTE]Модулю AzureResourceManager требуется командлет Add-AzureAccount. Файла параметров публикации недостаточно.

### Шаг 2. Выбор шаблона коллекции

Существует несколько способов создания группы ресурсов и ее ресурсов, но проще всего использовать шаблон группы ресурсов. *Шаблон группы ресурсов* — это строка JSON, определяющая ресурсы в группе ресурсов. Строка содержит местозаполнители, называемые "параметрами", для определяемых пользователем значений, например для имен и размеров.

В Azure размещается коллекция шаблонов групп ресурсов, а вы можете создавать собственные шаблоны с нуля или путем изменения шаблона из коллекции. В этом учебнике будет использоваться шаблон из коллекции.

Чтобы просмотреть все шаблоны в коллекции шаблонов групп ресурсов Azure, воспользуйтесь командлетом **Get-AzureResourceGroupGalleryTemplate**, однако эта команда возвращает большое количество шаблонов. Для просмотра меньшего числа шаблонов укажите параметр издателя.

Введите следующее в командной строке PowerShell:
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

Командлет возвращает список шаблонов коллекции, для которых издателем является корпорация Майкрософт. С помощью свойства **Identity** можно идентифицировать шаблон в командах.

Нас интересует шаблон Microsoft.WebSiteSQLDatabase.0.2.6-preview. При выполнении команды версия шаблона может немного отличаться в связи с выпуском новой версии. Используйте самую новую версию шаблона. Чтобы получить дополнительные сведения о шаблоне коллекции, используйте параметр **Identity**. Значение параметра Identity является идентификатором шаблона.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

Командлет возвращает объект вместе с более подробной информацией о шаблоне, включая сводку и описание.

Похоже, этот шаблон соответствует нашим нуждам. Давайте сохраним его на диск и рассмотрим более детально.

### Шаг 3. Изучение шаблона

Давайте сохраним шаблон в JSON-файл на диске. Этот шаг не является обязательным, но он упрощает просмотр шаблона. Чтобы сохранить шаблон, используйте командлет **Save-AzureResourceGroupGalleryTemplate**. Используйте параметр **Identity** для указания шаблона и параметр **Path** для указания пути на диске.

Save-AzureResourceGroupGalleryTemplate сохраняет шаблон и возвращает путь к JSON-файлу шаблона.

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


Файл шаблона можно просмотреть в текстовом редакторе, например в Блокноте. В каждом шаблоне имеются разделы **parameters** и **resources**.

Раздел **parameters** в шаблоне представляет собой набор параметров, которые определяются во всех ресурсах. Они включают в себя значения свойств, которые можно указать при настройке группы ресурсов.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Некоторые параметры имеют значение по умолчанию. При использовании шаблона для этих параметров не требуется указывать значения. Если значение не указано, используется значение по умолчанию.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

При использовании параметров с перечислимыми значениями допустимые значения указываются вместе с параметром. Например, параметр **sku** может принимать значения Free, Shared, Basic или Standard. Если значение для параметра **sku** не указывается, используется значение по умолчанию (Free).

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },


Заметьте, что параметр **administratorLoginPassword** использует не обычный текст, а защищенную строку. При указании значения для защищенной строки значение скрывается.

	"administratorLoginPassword": {
      "type": "securestring"
    },

В разделе **resources** перечислены ресурсы, создаваемые шаблоном. Данный шаблон создает сервер базы данных SQL и базу данных SQL, ферму серверов и веб-сайт, а также несколько параметров управления.
  
Определение каждого ресурса включает его свойства, такие как имя, тип и расположение, и параметры для определяемых пользователем значений. Например, в этом разделе шаблона определяется база данных SQL. Он включает параметры для имени базы данных ([parameters('databaseName')]), расположения сервера базы данных [parameters('serverLocation')] и свойства параметров сортировки [parameters('collation')].

    {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[parameters('serverLocation')]",
        "apiVersion": "2.0",
        "dependsOn": [
          "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


Почти все готово для использования шаблона, но прежде нам нужно найти расположения для каждого из ресурсов.

### Шаг 4. Получение расположений типов ресурсов

Большинство шаблонов предлагает указать расположение каждого ресурса, входящего в группу ресурсов. Каждый ресурс находится в центре обработки данных Azure, но не в каждом центре обработки данных Azure поддерживаются все типы ресурсов.

Выберите любое расположение, в котором поддерживается тип ресурса. Необязательно создавать все ресурсы группы ресурсов в одном расположении, в том же расположении; однако при наличии такой возможности ресурсы следует создавать в одном расположении, чтобы оптимизировать производительность. В частности, необходимо убедиться в том, что база данных находится в том же расположении, что и обращающееся к ней приложение.

Чтобы получить расположения, поддерживающие каждый тип ресурса, используйте командлет **Get-AzureLocation**. Чтобы ограничить выходные данные определенным типом ресурсов, например ResourceGroup, используйте следующую команду:

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

Результат должен быть аналогичен приведенному ниже:

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Теперь у нас есть сведения, необходимые для создания группы ресурсов.

### Шаг 5. Создание группы ресурсов
 
На этом шаге мы используем шаблон группы ресурсов для создания группы ресурсов. Для справки откройте JSON-файл New_WebSite_And_Database.json на диске и следуйте ему. Файл шаблона может оказаться очень полезным при определении передаваемых значений параметров, например правильного значения ApiVersion для ресурса.

Чтобы создать группу ресурсов, используйте командлет **New-AzureResourceGroup**.

В команде используются параметр **Name** для указания имени группы ресурсов и параметр **Location** для указания ее расположения. Используйте выходные данные командлета **Get-AzureLocation** для выбора расположения группы ресурсов. В нем используется параметр **GalleryTemplateIdentity** для указания шаблона коллекции.

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

Сразу после ввода имени шаблона командлет New-AzureResourceGroup выбирает шаблон, анализирует его и динамически добавляет параметры шаблона в команду. Таким образом можно легко указать значения параметров шаблона. А если вы забыли значение обязательного параметра, Windows PowerShell запрашивает его.

**Динамические параметры шаблона**

Чтобы получить параметры, введите знак минус (-) для указания имени параметра, а затем нажмите клавишу TAB. Или введите первые несколько букв имени параметра, например siteName, а затем нажмите клавишу TAB.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell завершает имя параметра. Для циклического перебора имен параметров повторно нажимайте клавишу TAB.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Введите имя веб-сайта и повторите процесс с использованием клавиши TAB для каждого параметра. Параметры со значением по умолчанию не являются обязательными. Чтобы принять значение по умолчанию, не включайте параметр в команду.

Если в шаблоне имеется параметр с перечислимыми значениями, например параметр sku в данном шаблоне, то для циклического перебора значений параметра нажимайте клавишу TAB.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

Ниже приведен пример команды New-AzureResourceGroup, которая задает только обязательные параметры шаблона и общий параметр **Verbose**. Заметьте, что параметр **administratorLoginPassword** не включен.

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

При вводе команды запрашивается отсутствующий обязательный параметр **administratorLoginPassword**. А после ввода пароля значение защищенной строки скрывается. Эта стратегия исключает риск, сопряженный с вводом пароля в виде обычного текста.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

Командлет **New-AzureResourceGroup** возвращает группу ресурсов, созданную и развернутую им.

Всего за несколько шагов мы создали и развернули ресурсы, необходимые для сложного веб-сайта. Шаблон из коллекции предоставил почти всю информацию, которая требовалась для выполнения этой задачи. И задачу оказалось легко автоматизировать.

## Получение сведений о ваших группах ресурсов

После создания группы ресурсов можно использовать командлеты в модуле AzureResourceManager для управления ими.

- Чтобы получить все группы ресурсов, входящие в вашу подписку, используйте командлет **Get-AzureResourceGroup**:

		PS C:\>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- Чтобы получить ресурсы, входящие в группу ресурсов, используйте командлет **Get-AzureResource** и его параметр ResourceGroupName. Без параметров командлет Get-AzureResource возвращает все ресурсы в подписке Azure.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Добавление ресурсов в группу

- Чтобы добавить ресурс в группу ресурсов, используйте командлет **New-AzureResource**. Эта команда добавляет новый веб-сайт в группу ресурсов TestRG. Эта команда является немного более сложной, поскольку в ней не используется шаблон. 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Чтобы добавить новое развертывание на основе шаблона в группу ресурсов, используйте команду **New-AzureResourceGroupDeployment**.

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Перемещение ресурса

Существующие ресурсы можно переместить в новую группу ресурсов. Примеры см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## Удаление группы ресурсов

- Чтобы удалить ресурс из группы ресурсов, используйте командлет **Remove-AzureResource**. Этот командлет удаляет ресурс, но не удаляет группу ресурсов.

	Эта команда удаляет веб-сайт TestSite2 из группы ресурсов TestRG.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- Чтобы удалить группу ресурсов, используйте командлет **Remove-AzureResourceGroup**. Этот командлет удаляет группу ресурсов и входящие в нее ресурсы.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Устранение неполадок группы ресурсов
Во время экспериментов с командлетами в модуле AzureResourceManager наверняка будут появляться ошибки. Чтобы устранить их, воспользуетесь подсказками в этом разделе.

### Предотвращение ошибок

Модуль AzureResourceManager включает командлеты, которые помогают избежать ошибок.


- **Get-AzureLocation**: этот командлет возвращает расположения, поддерживающие каждый тип ресурса. Прежде чем вводить расположение ресурса, с помощью этого командлета проверьте, поддерживает ли расположение тип ресурса.


- **Test-AzureResourceGroupTemplate**: протестируйте шаблон и его параметры перед использованием. Введите пользовательский шаблон или шаблон из коллекции и значения параметров шаблона, которые планируется использовать. Этот командлет проверяет внутреннюю непротиворечивость шаблона и соответствие набора значений параметров шаблону.



### Устранение ошибок

- **Get-AzureResourceGroupLog**: этот командлет возвращает записи журнала для каждого развертывания группы ресурсов. Если что-то идет не так, начните с проверки журналов развертывания. 

- **Verbose и Debug**: командлеты в модуле AzureResourceManager обращаются к интерфейсам API REST, которые выполняют фактическую работу. Чтобы увидеть сообщения, возвращаемые интерфейсами API, присвойте переменной $DebugPreference значение "Continue" и используйте общий параметр Verbose в командах. В сообщениях часто содержатся важные сведения о причинах ошибок.

- **Ваши учетные данные Azure не настроены или просрочены**: чтобы обновить учетные данные в сеансе Windows PowerShell, используйте командлет Add-AzureAccount. Учетных данных в файле параметров публикации недостаточно для командлетов в модуле AzureResourceManager.


## Дальнейшие действия
Приступая к работе

- [Общие сведения о диспетчере ресурсов Azure](./resource-group-overview.md)
- [Использование CLI Azure для Mac, Linux и Windows со средствами управления ресурсами Azure](./xplat-cli-azure-resource-manager.md)
- [Управление ресурсами Azure с помощью портала Azure](./resource-group-portal.md)

Создание и развертывание приложений

- [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md)
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](./resource-group-template-deploy.md)
- [Предсказуемое развертывание сложного приложения в Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Устранение неполадок при развертывании групп ресурсов в Azure](./resource-group-deploy-debug.md)
- [Функции шаблонов в диспетчере ресурсов Azure](./resource-group-template-functions.md)
- [Расширенные операции с шаблонами](./resource-group-advanced-template.md)

Упорядочение ресурсов

- [Использование тегов для организации ресурсов в Azure](./resource-group-using-tags.md)

Управление доступом и его аудит

- [Управление доступом к ресурсам и его контроль](./resource-group-rbac.md)
- [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](./resource-group-authenticate-service-principal.md)
- [Создание нового субъекта-службы Azure с помощью классического портала Azure](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO4-->