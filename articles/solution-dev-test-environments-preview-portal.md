<properties
   pageTitle="Среды разработки и тестирования | Microsoft Azure"
   description="Узнайте, как с помощью шаблонов диспетчера ресурсов Azure быстро и согласованно создавать и удалять среды разработки и тестирования."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Среды разработки и тестирования в Microsoft Azure

Перед развертыванием в производственной среде пользовательские приложения, как правило, развертываются в нескольких средах разработки и тестирования. Если среды создаются локально, вычислительные ресурсы выделяются или закупаются для каждого приложения в каждой среде. Среды часто включают несколько физических компьютеров или виртуальных машин в определенных конфигурациях, которые развертываются вручную или с помощью сложных скриптов автоматизации. Развертывание часто может занимать много времени, а конфигурации сред могут быть несогласованными.

## Сценарий ##

При подготовке сред разработки и тестирования в Microsoft Azure вы платите только за те ресурсы, которыми пользуетесь. В этой статье объясняется, как можно быстро и согласованно создавать, обслуживать и удалять среды разработки и тестирования с помощью шаблонов и файлов параметров, доступных в диспетчере ресурсов Azure.

![Сценарий](./media/solution-dev-test-environments-preview-portal/scenario.png)

Выше показаны три среды разработки и тестирования. В каждой есть веб-приложение и база данных SQL. Имена приложений и баз данных во всех средах отличаются. В этой статье объясняется, как с помощью шаблона развернуть один и тот же ресурс в разных средах и использовать уникальный файл параметров, чтобы задать различные конфигурации для ресурсов в разных средах.

Если вы незнакомы с основными понятиями диспетчера ресурсов Azure, рекомендуется перед продолжением прочитать статью [Обзор диспетчера ресурсов Azure](resource-group-overview.md).

Кроме того, вы также можете попробовать выполнить инструкции, приведенные в этом разделе, не прибегая к дополнительным материалам, чтобы быстро получить опыт работы с шаблонами диспетчера ресурсов Azure. В процессе выполнения инструкций у вас, несомненно, появятся вопросы. Выполнив инструкции от начала до конца, вы получите ответы на большинство из них, а на оставшиеся сможете ответить, ознакомившись с упомянутыми статьями или поэкспериментировав с используемыми значениями.

## Планирование использования ресурсов Azure
Разработав общий проект приложения, вы можете определить:

- Какие ресурсы Azure будут включены в приложение. Вы можете скомпилировать приложение и развернуть его как веб-приложение Azure с базой данных SQL Azure. Вы можете собрать приложение на виртуальных машинах с помощью PHP и MySQL, IIS и SQL Server или других компонентов. Чтобы решить, какие ресурсы Azure включить в свое приложение, ознакомьтесь со статьей [Сравнение службы приложений Azure, облачных служб и виртуальных машин](app-service-web/choose-web-site-cloud-service-vm.md).
- Каковы требования на уровне службы в отношении уровня доступности, безопасности и масштабирования, которым приложение должно соответствовать.

## Использование шаблонов диспетчера ресурсов Azure
Шаблон диспетчера ресурсов Azure определяет все ресурсы Azure, которые использует приложение. Существует несколько шаблонов, которые можно развернуть непосредственно из портала предварительной версии Azure, загрузить, изменить и сохранить вместе с кодом приложения в системе управления исходным кодом. Скорее всего, шаблон, содержащий ресурсы, которые вы захотите использовать в своем приложении, уже существует. Список доступных шаблонов можно найти в разделе [Шаблоны быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/) в репозитории GitHub.

В списке есть папка [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database). Так как многие пользовательские приложения включают веб-приложения и базу данных SQL, этот шаблон используется далее в этой статье в качестве примера использования шаблонов. В рамках этой статьи мы не будем рассматривать все, что можно создать и настроить с помощью этого шаблона. Если вы планируете использовать его для создания реальной среды в своей организации, то имеет смысл прочитать подробную информацию в статье [Подготовка веб-приложения с базой данных SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).

> [AZURE.NOTE]Шаблон можно развернуть в Azure напрямую, нажав кнопку «Развернуть в Azure», как описано в статье [Подготовка веб-приложения с базой данных SQL](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Указанная статья может оказаться полезной для изучения шаблонов, однако вряд ли поможет научиться изменять и сохранять шаблоны и значения параметров, а также вести учет их версий с помощью кода приложения. В данной статье мы рассмотрим, как с помощью кода приложения можно сохранять шаблоны и значения параметров и вести учет их версий.

  **Шаг 1.** Просмотрите содержимое файла [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) в папке 201-web-app-sql-database. Это файл шаблона диспетчера ресурсов Azure. В режиме просмотра нажмите кнопку [Raw](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json). Выделите все содержимое этого файла с помощью мыши и сохраните его на компьютере в файл TestApp1-Template.json.

В файле шаблона вы увидите раздел resources, который определяет, какие ресурсы Azure создаются с его помощью. Помимо прочего, этот шаблон создает ресурсы [веб-приложение Azure](app-service-web/app-service-web-overview.md) и [база данных SQL Azure](sql-database/sql-database-technical-overview.md).

Вы также увидите раздел parameters, определяющий параметры, которые можно настроить для каждого ресурса. У некоторых параметров, указанных в шаблоне, есть свойство defaultValue, а у других нет. При развертывании ресурсов Azure с помощью шаблона необходимо указать значения для всех параметров, у которых нет свойства defaultValue. Если не предоставить значения для параметров со свойством defaultValue, будет использоваться значение, указанное в свойстве defaultValue в шаблоне.

Шаблон определяет, какие ресурсы Azure создаются и какие параметры каждого ресурса можно настроить. Как правило, для сред тестирования и разработки требуется создать один и тот же набор ресурсов. Дополнительные сведения о создании собственных шаблонов см. в статье [Рекомендации по разработке шаблонов диспетчера ресурсов Azure](best-practices-resource-manager-design-templates.md).

## Создание файлов параметров

Возможно, во всех средах вы будете использовать одни и те же ресурсы Azure, но с разной конфигурацией. В этом случае вам пригодятся файлы параметров.

  **Шаг 2.** Просмотрите содержимое файла [azuredeploy parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) в папке 201-web-app-sql-database. Это файл параметров для файла шаблона, сохраненного на шаге 1. В режиме просмотра нажмите кнопку [Raw](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json). Выделите все содержимое этого файла с помощью мыши и сохраните его в трех отдельных файлах на своем компьютере:

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **Шаг 3.** Используя любой текстовый редактор или редактор JSON, измените файлы параметров для среды разработки и тестирования, созданные на шаге 2, заменив существующие значения на приведенные ниже.

  --TestApp1-Parameters-Development.json--

| Параметр | Описание |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | Central US |
| **serverName** | testapp1dev |
| **serverLocation** | Central US |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| Параметр | Описание |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | Central US |
| **serverName** | testapp1test |
| **serverLocation** | Central US |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

На шаге 6 мы создадим с помощью этих файлов параметров уникальные конфигурации веб-приложения Azure и базы данных SQL Azure в средах разработки и тестирования.

 **Шаг 4.** Измените файл параметров TestApp1-Parameters-Pre-Production.json, созданный на шаге 2. Замените все содержимое файла следующим кодом:

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

Параметры **sku** и **requestedServiceObjectiveName** параметры были *добавлены* в файл параметров для подготовительной среды, но не были добавлены в файлы параметров для сред разработки и тестирования. Это связано с тем, что для данных параметров в шаблоне есть значения по умолчанию, которые используются в средах разработки и тестирования. В подготовительной же среде задаются другие значения,

что позволяет проверить, можно ли будет их использовать в рабочей среде. Эти параметры связаны с используемыми приложением [планом хостинга веб-приложений](http://azure.microsoft.com/pricing/details/app-service/) Azure, **sku** и [базой данных SQL](http://azure.microsoft.com/pricing/details/sql-database/) Azure или **requestedServiceObjectiveName**. От SKU и целей службы зависят стоимость, доступные функции, а также метрики на уровне службы.

В следующей таблице перечислены указанные в шаблоне значения по умолчанию для этих параметров, а также значения, которые используются вместо значений по умолчанию в файле параметров для подготовительной среды.

| Параметр | Значение по умолчанию | Значение в файле параметров |
|---|---|---|
| **sku** | Free | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Создание сред
Все ресурсы Azure должны создаваться в [группе ресурсов Azure](azure-portal/resource-group-portal.md). Группы ресурсов позволяют объединять ресурсы Azure, чтобы ими можно было управлять как единым целым. Для групп ресурсов можно назначать [разрешения](resource-group-rbac.md), чтобы конкретные люди в вашей организации могли создавать, изменять, удалять или просматривать их и ресурсы в них. Предупреждения и сведения об оплате использования ресурсов в группе ресурсов можно просмотреть на [портале предварительной версии Azure](https://portal.azure.com). Группы ресурсов создаются в [расположении](http://azure.microsoft.com/regions/) Azure. В этой статье все ресурсы создаются в центральной части США (Central US). При создании реальных сред вы выбираете расположение, которое лучше всего отвечает вашим требованиям.

  **Шаг 5.** Создайте группы ресурсов для сред разработки и тестирования, используя один из следующих методов. Оба метода приводят к одному и тому же результату.

  **Метод 1.** Интерфейс командной строки Azure (CLI)

  Убедитесь, что среда CLI [установлена](xplat-cli-install.md) на компьютере под управлением Windows, OS X или Linux и что идентификатор вашей организации [связан](xplat-cli-connect.md) с подпиской Azure. Чтобы создать группу ресурсов для среды разработки, введите в командной строке CLI следующую команду.

	azure group create "TestApp1-Development" "Central US"

  В случае успешного выполнения команда вернет следующий результат:

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  Чтобы создать группу ресурсов для тестовой среды, введите следующую команду:

	azure group create "TestApp1-Test" "Central US"

  Чтобы создать группу ресурсов для подготовительной среды, введите следующую команду:

	azure group create "TestApp1-Pre-Production" "Central US"

  **Метод 2.** PowerShell

  Убедитесь, что среда PowerShell установлена на компьютере под управлением Windows и подключена к вашей подписке, как описано в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md). Чтобы создать группу ресурсов для среды разработки, введите в командной строке PowerShell следующую команду. При использовании предварительной версии Azure PowerShell 1.0 это команда **New-AzureRmResourceGroup**, как показано ниже. Если вы используете версию Azure PowerShell, выпущенную до предварительной версии 1.0, то используется команда **New-AzureResourceGroup**.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  В случае успешного выполнения команда вернет следующий результат:

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  Чтобы создать группу ресурсов для тестовой среды, введите следующую команду:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  Чтобы создать группу ресурсов для подготовительной среды, введите следующую команду:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **Шаг 6.** Разверните ресурсы Azure в группах ресурсов для каждой среды с помощью файла шаблона для приложения и файлов параметров для каждой среды. Для этого можно использовать один из описанных ниже методов. Оба метода приводят к одному и тому же результату.

  **Метод 1.** Интерфейс командной строки Azure (CLI)

  В командной строке CLI введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для среды разработки. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  В случае успешного выполнения команда вернет следующий результат:

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  В случае неудачи устраните причины всех сообщений об ошибках и повторите попытку. Проблемы чаще всего связаны с использованием значений параметров, которые не соответствуют правилам именования ресурсов Azure. Другие советы по устранению неполадок см. в статье [Устранение неполадок при развертывании групп ресурсов в Azure](virtual-machines/resource-group-deploy-debug.md).

  В командной строке CLI введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для тестовой среды. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  В командной строке CLI введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для подготовительной среды. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **Метод 2.** PowerShell

  В командной строке PowerShell введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для среды разработки. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах. При использовании предварительной версии Azure PowerShell 1.0 это команда **New-AzureRmResourceGroupDeployment**, как показано ниже. Если вы используете версию Azure PowerShell, выпущенную до предварительной версии 1.0, то используется команда **New-AzureResourceGroupDeployment**.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  В случае успешного выполнения команда вернет следующий результат:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  В случае неудачи устраните причины всех сообщений об ошибках и повторите попытку. Проблемы чаще всего связаны с использованием значений параметров, которые не соответствуют правилам именования ресурсов Azure. Другие советы по устранению неполадок см. в статье [Устранение неполадок при развертывании групп ресурсов в Azure](virtual-machines/resource-group-deploy-debug.md).

  В командной строке PowerShell введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для тестовой среды. При этом [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  В командной строке PowerShell введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для подготовительной среды. При этом [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Для файла шаблона и файлов параметров можно создавать версии и вести их вместе с кодом приложения в системе управления исходным кодом. Вы также можете сохранить использованные выше команды в файлы скриптов и сохранить их вместе с кодом.

## Поддержка сред
В процессе разработки конфигурация ресурсов Azure в одной среде может быть изменена — случайно или преднамеренно — независимо от других сред. Это может приводить к дополнительным проблемам в рамках цикла разработки приложения.

  **Шаг 7.** Измените среды. Откройте [портал предварительной версии Azure](https://portal.azure.com) и выполните вход с учетной записью, с помощью которой вы выполняли предыдущие действия. Выберите «Просмотреть все» -> «Группы ресурсов» (возможно, потребуется прокрутить колонку вниз до групп ресурсов), как показано на следующем рисунке. Вы увидите три группы ресурсов, созданные с помощью методов, описанных на предыдущих этапах. Щелкните группу ресурсов TestApp1-Development (см. ниже).

  ![Портал](./media/solution-dev-test-environments-preview-portal/portal1.png)

  После выбора группы ресурсов TestApp1-Development откроется колонка, в которой перечислены ресурсы, созданные на основе шаблона при развертывании группы ресурсов на предыдущем шаге. Удалите ресурс TestApp1Dev Web App, выбрав TestApp1Dev -> «Удалить» (см. ниже).

  ![Портал](./media/solution-dev-test-environments-preview-portal/portal2.png)

  Выберите «Да», когда появится запрос на подтверждение удаления ресурса. Состав группы ресурсов теперь отличается от требуемого. Вы можете дополнительно поэкспериментировать с удалением нескольких ресурсов из разных групп ресурсов или даже с изменением параметров конфигурации некоторых ресурсов.

> [AZURE.NOTE]Помимо портала предварительной версии Azure, для удаления ресурса из группы ресурсов вы можете также использовать команду [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) в PowerShell или команду azure resource delete в CLI.

  **Шаг 8.** Повторно разверните среду для групп ресурсов с помощью тех же команд, что использовались на шаге 6, при этом замените Deployment1 на Deployment2. Вы увидите, что все ресурсы из шаблона снова появились в группе ресурсов TestApp1-Development (см. раздел «Сводка» на следующем рисунке). Одним из преимуществ развертывания среды с помощью шаблонов диспетчера ресурсов Azure является возможность в любое время быстро повторно развернуть любое известное состояние среды.

  ![Портал](./media/solution-dev-test-environments-preview-portal/portal3.png)

  Если щелкнуть текст под заголовком «Последнее развертывание» (см. рисунок), можно увидеть колонку с историей развертывания группы ресурсов. Поскольку для первого развертывания использовалось имя Deployment1, а для второго — Deployment2, будут отображаться две записи. Щелкнув конкретное развертывание, вы увидите колонку с его результатами.

## Удаление сред
После завершения работы со средами их следует удалить, чтобы не оплачивать ресурсы Azure, которые больше не используются. Удалить среду проще, чем создать. На предыдущих шагах для каждой среды были созданы отдельные группы ресурсов Azure. При удалении группы ресурсов удаляются также все ресурсы, входящие в нее. Любой из описанных ниже методов приведет к удалению ранее развернутых сред (групп ресурсов) и всех входящих в них ресурсов Azure.

  **Шаг 9.** Удалите среды одним из описанных ниже способов. Оба метода приводят к одному и тому же результату.

  **Метод 1. Azure CLI**

  Введите следующую команду в командной строке CLI:

	azure group delete "TestApp1-Development"

  Если при появлении запроса ввести «y», команда вернет следующий результат:

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  Введите следующую команду в командной строке CLI, чтобы удалить остальные среды:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **Метод 2.** PowerShell

  В предварительной версии Azure PowerShell 1.0 для удаления группы ресурсов используется команда **Remove-AzureRmResourceGroup**, как показано ниже. Если вы используете версию Azure PowerShell, выпущенную до предварительной версии 1.0, то используется команда **Remove-AzureResourceGroup**. Введите следующую команду в командной строке PowerShell:

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  Если при появлении запроса ввести «y», команда вернет следующий результат:

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  Введите следующую команду в командной строке PowerShell, чтобы удалить остальные среды:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

Вне зависимости от того, какой метод используется, после выполнения команд группы ресурсов и все ресурсы в них будут удалены и вам не придется за них платить.

Чтобы свести к минимуму затраты на использование ресурсов Azure во время разработки приложения, можно использовать [службу автоматизации Azure](automation/automation-intro.md) и запланировать задания для следующих операций:

- остановка виртуальных машин в конце каждого дня и их повторный запуск в начале каждого дня;
- удаление всех сред в конце каждого дня и их повторное создание в начале каждого дня.
 
Вы узнали, как можно с легкостью создавать, поддерживать и удалять среды разработки и тестирования. Теперь можно перейти к экспериментам с описанными процедурами и значениями, а также к изучению упомянутых в этом разделе статей.

## Дальнейшие действия

- [Делегирование администрирования](role-based-access-control-configure.md) ресурсами в разных средах посредством назначения пользователям и группам Microsoft Azure AD определенных ролей с привилегиями на выполнения определенного набора операций с ресурсами Azure.
- [Назначение тегов](resource-group-using-tags.md) отдельным ресурсам или группам ресурсов для каждой среды. Вы можете добавить тег «Environment» для групп ресурсов и присвоить ему значение, соответствующее имени конкретной среды. Теги особенно помогут, когда требуется организовать ресурсы для выставления счетов или управления.
- Мониторинг предупреждений и выставление счетов за использование ресурсов в группе ресурсов с помощью [портала предварительной версии Azure](https://portal.azure.com).

## Дополнительные ресурсы

- [Создавайте и развертывайте шаблоны диспетчера ресурсов Azure в Visual Studio](http://msdn.microsoft.com/library/azure/Dn872471.aspx) с помощью пакета SDK для Azure 2.6.
- Создайте приложение с помощью [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), [Visual Studio Code](http://www.visualstudio.com/products/code-vs) или [Web Matrix](http://www.microsoft.com/web/webmatrix/).
- [Разверните веб-приложение](app-service-web/web-sites-deploy.md) в созданных средах.
- Используйте [управление выпусками в Visual Studio](http://msdn.microsoft.com/Library/vs/alm/Release/overview), чтобы создавать управляемые конвейеры непрерывной разработки для регулярных и частых выпусков новых версий.
- Запросите приглашение на использование предварительной версии [лаборатории для разработки и тестирования Azure](http://azure.microsoft.com/campaigns/devtest-lab/). Она позволяет управлять средами разработки и тестирования с помощью шаблонов, а также настраивать квоты и политики для использования внутри организации.

<!---HONumber=Nov15_HO2-->