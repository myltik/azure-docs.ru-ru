<properties pageTitle="Использование Windows PowerShell с диспетчером ресурсов" metaKeywords="диспетчер ресурсов, PowerShell, Azure PowerShell" description="Используйте Windows PowerShell для создания группы ресурсов" metaCanonical="" services="" documentationCenter="" title="Использование Windows PowerShell с диспетчером ресурсов" authors="juneb" solutions="" manager="mbaldwin" editor="mollybos" />

# Использование Windows PowerShell с диспетчером ресурсов #

Диспетчер ресурсов предлагает кардинально новый способ представления ресурсов Azure. Вместо того чтобы создавать отдельные ресурсы и управлять ими, вы для начала представляете себе сложную службу, такую как блог, коллекция фотографий, портал SharePoint или вики-страница. Вы используете в качестве шаблона модель ресурса, предоставляемую службой, чтобы создать группу ресурсов, в которую входят ресурсы, необходимые для поддержки службы. Затем при развертывании и управлении можно использовать группу ресурсов как логическую единицу. 

В этом учебнике вы узнаете, как использовать Windows PowerShell с диспетчером ресурсов для Microsoft Azure. Здесь рассматривается процесс создания и развертывания группы ресурсов для размещенного в Azure веб-сайта (или веб-приложения) с базой данных SQL и всеми ресурсами, которые необходимы для его поддержки.

**Предполагаемое время выполнения:** 15 минут


## Предварительные требования ##

Чтобы использовать Windows PowerShell с диспетчером ресурсов, необходимо иметь следующее:

- Windows PowerShell версии 3.0 или более поздней. Чтобы найти версию Windows PowerShell, введите:`$PSVersionTable` и убедитесь, что для `PSVersion` указано значение 3.0 или более поздняя версия. Сведения об установке более новой версии см. в разделе [Windows Management Framework 3.0 ](http://www.microsoft.com/ru-ru/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/ru-ru/download/details.aspx?id=40855).
	
- Azure PowerShell версии 0.8.0 или более поздней. Сведения об установке последней версии и связывании ее с подпиской Azure см. в разделе [Установка и настройка Windows Azure PowerShell](http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/).

Этот учебник предназначен для начинающих пользователей Windows PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell см. в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/ru-ru/library/hh857337.aspx).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

	Get-Help Add-AzureAccount -Detailed

## В этом учебнике рассматриваются следующие темы: ##
* [О модулях Azure PowerShell](#about)
* [Создание группы ресурсов](#create)
* [Управление группой ресурсов](#manage)
* [Устранение неполадок группы ресурсов](#troubleshoot)
* [Дальнейшие действия](#next)



##<a id="about"></a>О модулях Azure PowerShell ##
Начиная с версии 0.8.0, в установку Azure PowerShell включены три модуля Windows PowerShell:

- **Azure**. Включает традиционные командлеты для управления отдельными ресурсами, такими как учетные записи хранения, веб-сайты, базы данных, виртуальные машины и службы мультимедиа. Дополнительные сведения см. в разделе [Командлеты управления службой Azure](http://msdn.microsoft.com/ru-ru/library/jj152841.aspx).

- **AzureResourceManager**. Включает командлеты для создания, управления и развертывания ресурсов Azure для группы ресурсов. Дополнительные сведения см. в разделе [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765).

- **AzureProfile**. Включает командлеты, общие для обоих модулей, например Add-AzureAccount, Get-AzureSubscription и Switch-AzureMode. Дополнительные сведения см. в разделе [Командлеты профиля Azure](http://go.microsoft.com/fwlink/?LinkID=394766).

> [ WACOM.NOTE] Модуль диспетчера ресурсов Azure в настоящее время находится на стадии предварительной версии. Он может не обеспечивать те же возможности управления, что и модуль управления службой Azure. 

Модуль Azure и модуль диспетчера ресурсов Azure не предназначены для использования в одном сеансе Windows PowerShell. Чтобы упростить переключение между ними, мы добавили новый командлет **Switch-AzureMode** в модуль профиля Azure.

При использовании Azure PowerShell командлеты в модуле Azure и модуле профиля Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет Switch-AzureMode. Он удаляет модуль Azure из сеанса и импортирует модули диспетчера ресурсов Azure (и профиля Azure).

Чтобы перейти в модуль AzureResoureManager, введите:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Чтобы вернуться в модуль Azure, введите:

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

По умолчанию действие командлета Switch-AzureMode распространяется только на текущий сеанс. Для эффективного переключения во всех сеансах Windows PowerShell используйте параметр **Global** командлета Switch-AzureMode.

Чтобы получить справку по командлету Switch-AzureMode, введите `Get-Help Switch-AzureMode` или см. раздел [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
Чтобы получить список командлетов в модуле AzureResourceManager вместе с краткой справочной информацией, введите: 

	PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

	Name                                   Synopsis
	----                                   --------
	Get-AzureLocation                      Возвращает расположения центров обработки данных Azure и поддерживаемые ими типы ресурсов
	Get-AzureResource                      Возвращает ресурсы Azure
	Get-AzureResourceGroup                 Возвращает группы ресурсов Azure
	Get-AzureResourceGroupDeployment       Возвращает развертывания в группе ресурсов.
	Get-AzureResourceGroupGalleryTemplate  Возвращает шаблоны групп ресурсов в коллекции
	Get-AzureResourceGroupLog              Возвращает журнал развертывания для группы ресурсов
	New-AzureResource                      Создает новый ресурс в группе ресурсов
	New-AzureResourceGroup                 Создает группу ресурсов Azure и ресурсы в ней
	New-AzureResourceGroupDeployment       Добавляет развертывание Azure в группу ресурсов.
	Remove-AzureResource                   Удаляет ресурс.
	Remove-AzureResourceGroup              Удаляет группу ресурсов.
	Save-AzureResourceGroupGalleryTemplate Сохраняет шаблон коллекции в JSON-файл
	Set-AzureResource                      Изменяет свойства ресурса Azure.
	Stop-AzureResourceGroupDeployment      Отменяет развертывание группы ресурсов
	Test-AzureResourceGroupTemplate        Обнаруживает ошибки в шаблоне группы ресурсов или параметрах шаблона


Чтобы получить полную справку для командлета, введите команду в формате:

	Get-Help <cmdlet-name> -Full

Например: 

	Get-Help Get-AzureLocation -Full

  
#<a id="create"></a> Создание группы ресурсов для веб-сайта и базы данных#

В этом разделе учебника рассматривается процесс создания и развертывания группы ресурсов для веб-сайта с базой данных SQL. 

Для выполнения этой задачи не нужно быть экспертом по Azure, SQL, веб-сайтам или управлению ресурсами. Шаблоны предоставляют модель группы ресурсов со всеми ресурсами, которые вам могут понадобиться. А поскольку мы используем Windows PowerShell для автоматизации задач, вы можете рассматривать этот процесс как модель для создания сценариев более масштабных задач.

## Шаг 1. Переход в диспетчер ресурсов Azure 
1. Запустите Windows PowerShell. Вы можете использовать любую основную программу, например консоль Windows PowerShell или интегрированную среду сценариев Windows PowerShell.

2. Используйте командлет **Switch-AzureMode** для импорта командлетов в модулях AzureResourceManager и AzureProfile. 

	`PS C:\>Switch-AzureMode AzureResourceManager`

3. Чтобы добавить учетную запись Azure в сеанс Windows PowerShell, используйте командлет **Add-AzureAccount**. 

    `PS C:\> Add-AzureAccount`

Командлет запрашивает адрес электронной почты и пароль. Затем он загружает параметры учетной записи, чтобы они были доступны в Windows PowerShell. 

Срок действия параметров учетной записи истекает, поэтому необходимо их периодически обновлять. Чтобы обновить параметры учетной записи, еще раз выполните командлет **Add-AzureAccount**. 

> [ WACOM.NOTE] Модулю AzureResourceManager требуется командлет Add-AzureAccount. Файла параметров публикации недостаточно.     



## Шаг 2. Выбор шаблона коллекции ##

Существует несколько способов создания группы ресурсов и ее ресурсов, но проще всего использовать шаблон группы ресурсов. *Шаблон группы ресурсов* — это строка JSON, определяющая ресурсы в группе ресурсов. Строка содержит местозаполнители, называемые "параметрами", для определяемых пользователем значений, например для имен и размеров.

В Azure размещается коллекция шаблонов групп ресурсов, а вы можете создавать собственные шаблоны с нуля или путем изменения шаблона из коллекции. В этом учебнике будет использоваться шаблон из коллекции. 

Для поиска шаблона в коллекции шаблонов групп ресурсов Azure используйте командлет **Get-AzureResourceGroupGalleryTemplate**.  

В командной строке Windows PowerShell введите:
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate

Командлет возвращает список шаблонов коллекции вместе со свойствами Publisher и Identity. С помощью свойства **Identity** можно идентифицировать шаблон в командах.

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
	Joomla          Joomla.Joomla.0.1.0-preview1
	Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
	...

ПОДСКАЗКА. Для повторного вызова последней команды нажмите клавишу со стрелкой вверх.

Нас интересует шаблон Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json. Чтобы получить дополнительные сведения о шаблоне коллекции, используйте параметр **Identity**. Значение параметра Identity является идентификатором шаблона.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json

Командлет возвращает объект вместе с более подробной информацией о шаблоне, включая описание.

	<p>Служба веб-сайтов Windows Azure предлагает безопасные и гибкие 
	возможности для разработки, развертывания и масштабирования веб-приложений 
	любого размера. Используйте имеющиеся у вас средства для создания 
	и развертывания приложений, не заботясь об управлении инфраструктурой.</p>
 
Похоже, этот шаблон соответствует нашим нуждам. Давайте сохраним его на диск и рассмотрим более детально.
 
## Шаг 3. Изучение шаблона

Давайте сохраним шаблон в JSON-файл на диске. Этот шаг не является обязательным, но он упрощает просмотр шаблона. Чтобы сохранить шаблон, используйте командлет **Save-AzureResourceGroupGalleryTemplate**. Используйте параметр **Identity** для указания шаблона и параметр **Path** для указания пути на диске.

Save-AzureResourceGroupGalleryTemplate сохраняет шаблон и возвращает путь к JSON-файлу шаблона. 

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json -Path D:\Azure\Templates

	Path
	----
	D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json


Файл шаблона можно просмотреть в текстовом редакторе, например в Блокноте. В каждом шаблоне имеются разделы **resources** и **parameters**.

В разделе **resources** перечислены ресурсы, создаваемые шаблоном. Данный шаблон создает сервер базы данных SQL и базу данных SQL, ферму серверов и веб-сайт, а также несколько параметров управления.
  
Определение каждого ресурса включает его свойства, такие как имя, тип и расположение, и параметры для определяемых пользователем значений. Например, в этом разделе шаблона определяется база данных SQL. Он включает параметры для имени базы данных ([parameters('databaseName')]), расположения сервера базы данных [parameters('serverLocation')]и свойства параметров сортировки [parameters('collation')].

		{
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

Раздел **parameters** в шаблоне представляет собой набор параметров, которые определяются во всех ресурсах. Он содержит свойства имени базы данных (databaseName), расположения сервера (serverLocation) и сортировки (collation).

	"parameters": {
	...    

    "serverLocation": {
      "type": "string"
    }, 
	...

    "databaseName": {
      "type": "string"
    },
    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

Некоторые параметры имеют значение по умолчанию. При использовании шаблона для этих параметров не требуется указывать значения. Если значение не указано, используется значение по умолчанию. 

	"collation": {
	      "type": "string",
	      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
	    }

При использовании параметров с перечислимыми значениями, допустимые значения указываются вместе с параметром. Например, параметр **sku** может принимать значения Free, Shared, Basic или Standard. Если значение для параметра **sku** не указывается, используется значение по умолчанию (Free).

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


Почти все готово для использования шаблона, но прежде нам нужно найти расположения для каждого из ресурсов.

## Шаг 4. Получение расположений типов ресурсов

Большинство шаблонов предлагает указать расположение каждого ресурса, входящего в группу ресурсов. Каждый ресурс находится в центре обработки данных Azure, но не в каждом центре обработки данных Azure поддерживаются все типы ресурсов. 

Выберите любое расположение, в котором поддерживается тип ресурса. Ресурсы, входящие в группу ресурсов, не обязаны находиться в одном расположении или в том же расположении, что и группа ресурсов или подписка.

Чтобы получить расположения, поддерживающие каждый тип ресурса, используйте командлет **Get-AzureLocation**. Ниже приведен фрагмент выходных данных. (Эти выходные данные у вас могут отличаться. Детали могут со временем меняться.)

	Name                                 Locations
	----                                 ---------
	ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
										 South Central US, Central US, North Europe, West Europe

	Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
	                                     East, Japan West, North Central US, North Europe, South Central US,
	                                     Southeast Asia, West Europe, West US

	Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
	                                     Central US, North Europe, West Europe, West US

	Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
	                                     Central US, North Europe, West Europe, West US

Теперь у нас есть сведения, необходимые для создания группы ресурсов.

## Шаг 5. Создание группы ресурсов
 
На этом шаге мы используем шаблон группы ресурсов для создания группы ресурсов. Для справки откройте JSON-файл Microsoft.WebSiteSQLDatabase.0.1.0-preview1 на диске и следуйте ему. 

Чтобы создать группу ресурсов, используйте командлет **New-AzureResourceGroup**.

В команде используются параметр **Name** для указания имени группы ресурсов и параметр **Location** для указания ее расположения. Используйте выходные данные командлета **Get-AzureLocation** для выбора расположения группы ресурсов. В нем используется параметр **GalleryTemplateIdentity** для указания шаблона коллекции.

	PS C:\> New-AzureResourceGroup ` 
			-Name TestRG1 `
			-Location "East Asia" `
			-GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

Сразу после ввода имени шаблона командлет New-AzureResourceGroup выбирает шаблон, анализирует его и динамически добавляет параметры шаблона в команду. Таким образом можно легко указать значения параметров шаблона. А если вы забыли значение обязательного параметра, Windows PowerShell запрашивает его.

**Динамические параметры шаблона**

Чтобы получить параметры, введите знак минус (-) для указания имени параметра, а затем нажмите клавишу TAB. Или введите первые несколько букв имени параметра, например siteName, а затем нажмите клавишу TAB. 

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-si<TAB>

Windows PowerShell дополняет имя параметра. Для циклического перебора имен параметров повторно нажимайте клавишу TAB.

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName 

Введите имя веб-сайта и повторите процесс с использованием клавиши TAB для каждого параметра. Параметры со значением по умолчанию не являются обязательными. Чтобы принять значение по умолчанию, не включайте параметр в команду. 

Если в шаблоне имеется параметр с перечислимыми значениями, например параметр sku в данном шаблоне, то для циклического перебора значений параметра нажимайте клавишу TAB.

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku <TAB>

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku Free<TAB>

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku Basic<TAB>

Ниже приведен пример команды New-AzureResourceGroup, которая задает только обязательные параметры шаблона и общий параметр **Verbose**. Заметьте, что параметр **administratorLoginPassword** не включен. (Обратный апостроф (`) является символом продолжения строки в Windows PowerShell.)

	PS C:\> New-AzureResourceGroup 
	-Name TestRG `
	-Location "East Asia" `
	-GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
	-siteName TestSite `
	-hostingPlanName TestPlan `
	-siteLocation "North Europe" `
	-serverName testserver `
	-serverLocation "West US" `
	-administratorLogin Admin01 `
	-databaseName TestDB `
	-Verbose

При вводе команды запрашивается отсутствующий обязательный параметр **administratorLoginPassword**. А после ввода пароля значение защищенной строки скрывается. Эта стратегия исключает риск, сопряженный с вводом пароля в виде обычного текста.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

Команда **New-AzureResourcGroup** возвращает группу ресурсов, которая была создана и развернута. Ниже приведены результаты выполнения команды, включая подробные выходные данные.

	VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
	VERBOSE: 3:47:30 PM - Template is valid.
	VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
	using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
	VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
	VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
	succeeded
	VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
	VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
	provisioning status is succeeded
	VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
	'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
	VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
	VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
	VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
	status is succeeded
	VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded
	
	
	ResourceGroupName : TestRG
	Location          : eastasia
	ProvisioningState : Succeeded
	Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus


Всего за несколько шагов мы создали и развернули ресурсы, необходимые для сложного веб-сайта. 
Шаблон из коллекции предоставил почти всю информацию, которая требовалась для выполнения этой задачи.
И задачу оказалось легко автоматизировать. 

#<a id="manage"></a> Управление группой ресурсов

После создания группы ресурсов можно использовать командлеты в модуле AzureResourceManager для управления группой ресурсов, ее изменения, добавления в нее ресурсов или ее удаления.

- Чтобы получить группы ресурсов, входящие в вашу подписку, используйте командлет **Get-AzureResourceGroup**.

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Resources         :
	                    Name                   Type                                  Location
	                    =====================  ====================================  =========
	                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
	                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	                    TestSite               microsoft.insights/components         centralus
	                    testserver             Microsoft.Sql/servers                 westus
	                    TestDB                 Microsoft.Sql/servers/databases       westus
	                    TestPlan               Microsoft.Web/serverFarms             westus
	                    TestSite               Microsoft.Web/sites                   westus

- Чтобы получить ресурсы, входящие в группу ресурсов, используйте командлет **GetAzureResource** и его параметр ResourceGroupName. Без параметров командлет Get-AzureResource возвращает все ресурсы в подписке Azure.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		Name                   ResourceType                          Location
		----                   ------------                          --------
		ServerErrors-TestSite  microsoft.insights/alertrules         eastus
	    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	    TestSite               microsoft.insights/components         centralus
	    testserver             Microsoft.Sql/servers                 westus
	    TestDB                 Microsoft.Sql/servers/databases       westus
	    TestPlan               Microsoft.Web/serverFarms             westus
	    TestSite               Microsoft.Web/sites                   westus



- Чтобы добавить ресурс в группу ресурсов, используйте командлет **New-AzureResource**. Эта команда добавляет новый веб-сайт в группу ресурсов TestRG. Эта команда является немного более сложной, поскольку в ней не используется шаблон. 

		PS C:\>New-AzureResource -Name TestSite2 `
		-Location "North Europe" `
		-ResourceGroupName TestRG `
		-ResourceType "Microsoft.Web/sites" `
		-ApiVersion 2004-04-01 `
		-PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Чтобы добавить новое развертывание на основе шаблона в группу ресурсов, используйте команду **New-AzureResourceGroupDeployment**. 

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteMode Limited `
		-computeMode Dedicated `
		-siteLocation "North Europe" 
		-subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
		-resourceGroup TestRG


- Чтобы удалить ресурс из группы ресурсов, используйте командлет **Remove-AzureResource**. Этот командлет удаляет ресурс, но не удаляет группу ресурсов.

	Эта команда удаляет веб-сайт TestSite2 из группы ресурсов TestRG.

		Remove-AzureResource -Name TestSite2 `
			-Location "North Europe" `
			-ResourceGroupName TestRG `
			-ResourceType "Microsoft.Web/sites" `
			-ApiVersion 2004-04-01

- Чтобы удалить группу ресурсов, используйте командлет **Remove-AzureResourceGroup**. Этот командлет удаляет группу ресурсов и входящие в нее ресурсы.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


#<a id="troubleshoot"></a> Устранение неполадок группы ресурсов
Во время экспериментов с командлетами в модуле AzureResourceManager наверняка будут появляться ошибки. Чтобы устранить их, воспользуетесь подсказками в этом разделе.

## Предотвращение ошибок

Модуль AzureResourceManager включает командлеты, которые помогают избежать ошибок.


- **Get-AzureLocation**. Этот командлет возвращает расположения, поддерживающие каждый тип ресурса. Прежде чем вводить расположение ресурса, с помощью этого командлета проверьте, поддерживает ли расположение тип ресурса.


- **Test-AzureResourceGroupTemplate**. Протестируйте шаблон и параметры шаблона перед использованием. Введите пользовательский шаблон или шаблон из коллекции и значения параметров шаблона, которые планируется использовать. Этот командлет проверяет внутреннюю непротиворечивость шаблона и соответствие набора значений параметров шаблону. 



## Устранение ошибок

- **Get-AzureResourceGroupLog**. Этот командлет возвращает записи журнала для каждого развертывания группы ресурсов. Если что-то идет не так, начните с проверки журналов развертывания. 

- **Verbose и Debug**. Командлеты в модуле AzureResourceManager обращаются к интерфейсам API REST, которые выполняют фактическую работу. Чтобы увидеть сообщения, возвращаемые интерфейсами API, присвойте переменной $DebugPreference значение "Continue" и используйте общий параметр Verbose в командах. В сообщениях часто содержатся важные сведения о причинах ошибок.

- **Ваши учетные данные Azure не настроены или просрочены**. Чтобы обновить учетные данные в сеансе Windows PowerShell, используйте командлет Add-AzureAccount. Учетных данных в файле параметров публикации недостаточно для командлетов в модуле AzureResourceManager.


#<a id="next"></a> Дальнейшие действия
Для получения дополнительных сведений об использовании Windows PowerShell с диспетчером ресурсов, ознакомьтесь со следующими ресурсами:
 
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): сведения об использовании командлетов в модуле AzureResourceManager.
- [Блог Azure](http://blogs.msdn.com/windowsazure): сведения о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): сведения о новых возможностях в Windows PowerShell.
- [Блог "Hey, Scripting Guy!"](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества.
- [Использование межплатформенного интерфейса командной строки Azure с диспетчером ресурсов](http://www.windowsazure.com/ru-ru/documentation/articles/xplat-cli-azure-resource-manager/): альтернативные способы автоматизации операций диспетчера ресурсов.

