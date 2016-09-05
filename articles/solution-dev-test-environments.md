.<properties
   pageTitle="Среды разработки и тестирования | Microsoft Azure"
   description="Узнайте, как с помощью шаблонов диспетчера ресурсов Azure быстро и согласованно создавать и удалять среды разработки и тестирования."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="jdial"/>

# Среды разработки и тестирования в Microsoft Azure

Перед развертыванием в производственной среде пользовательские приложения, как правило, развертываются в нескольких средах разработки и тестирования. Если среды создаются локально, вычислительные ресурсы выделяются или закупаются для каждого приложения в каждой среде. Среды часто включают несколько физических компьютеров или виртуальных машин в определенных конфигурациях, которые развертываются вручную или с помощью сложных скриптов автоматизации. Развертывание часто может занимать много времени, а конфигурации сред могут быть несогласованными.

## Сценарий ##

При подготовке сред разработки и тестирования в Microsoft Azure вы платите только за те ресурсы, которыми пользуетесь. В этой статье объясняется, как можно быстро и согласованно создавать, обслуживать и удалять среды разработки и тестирования с помощью шаблонов и файлов параметров, доступных в диспетчере ресурсов Azure.

![Сценарий](./media/solution-dev-test-environments/scenario.png)

Выше показаны три среды разработки и тестирования. В каждой из них есть веб-приложение и ресурсы базы данных SQL, которые указываются в файле шаблона. Имена приложения и базы данных в средах отличаются. Они указаны в уникальных файлах параметров для каждой среды.

Если вы незнакомы с основными понятиями диспетчера ресурсов Azure, рекомендуется перед продолжением прочитать статью [Обзор диспетчера ресурсов Azure](resource-group-overview.md).

Кроме того, вы также можете попробовать выполнить инструкции, приведенные в этом разделе, не прибегая к дополнительным материалам, чтобы быстро получить опыт работы с шаблонами диспетчера ресурсов Azure. Выполнив инструкции от начала до конца, вы получите ответы на большинство вопросов, возникших вначале, а на оставшиеся сможете ответить, ознакомившись с упомянутыми статьями или поэкспериментировав с некоторыми шагами.

## Планирование использования ресурсов Azure
Разработав общий проект приложения, вы можете определить:

- Какие ресурсы Azure будут включены в приложение. Вы можете скомпилировать приложение и развернуть его как веб-приложение Azure с базой данных SQL Azure. Вы можете собрать приложение на виртуальных машинах с помощью PHP и MySQL, IIS и SQL Server или других компонентов. Чтобы решить, какие ресурсы Azure включить в свое приложение, ознакомьтесь со статьей [Сравнение службы приложений Azure, облачных служб и виртуальных машин](app-service-web/choose-web-site-cloud-service-vm.md).
- Каковы требования на уровне службы в отношении уровня доступности, безопасности и масштабирования, которым приложение должно соответствовать.

## Скачивание существующего шаблона
Шаблон диспетчера ресурсов Azure определяет все ресурсы Azure, которые использует приложение. Существует несколько шаблонов, которые можно развернуть прямо из портала Azure, а также скачать, изменить и сохранить вместе с кодом приложения в системе управления исходным кодом. Выполните следующие шаги, чтобы скачать существующий шаблон.

1. Доступные шаблоны можно найти в репозитории GitHub в разделе [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates/) (Шаблоны быстрого запуска Azure). В списке есть папка [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database). Так как многие пользовательские приложения включают веб-приложения и базу данных SQL, этот шаблон используется далее в этой статье в качестве примера использования шаблонов. В рамках этой статьи мы не будем рассматривать все, что можно создать и настроить с помощью этого шаблона. Если вы планируете использовать его для создания реальной среды в своей организации, то имеет смысл прочитать подробную информацию в статье [Подготовка веб-приложения с базой данных SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md). Примечание: эта статья написана для версии шаблона [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) за декабрь 2015 г. Все ссылки на шаблоны и файлы параметров в статье ведут именно на эту версию шаблона.
2. В папке 201-web-app-sql-database щелкните файл [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json), чтобы просмотреть его содержимое. Это файл шаблона диспетчера ресурсов Azure.
3. В режиме просмотра нажмите кнопку [Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json).
4. Выделите содержимое этого файла с помощью мыши и сохраните его на компьютере в файле TestApp1-Template.json.
5. Проверьте содержимое шаблона и обратите внимание на следующее:
 - Раздел **resources**. В этом разделе определены типы ресурсов Azure, которые создает этот шаблон. Помимо прочего, этот шаблон создает ресурсы [веб-приложение Azure](app-service-web/app-service-web-overview.md) и [база данных SQL Azure](sql-database/sql-database-technical-overview.md). Если вы используете веб-серверы и серверы SQL Server на виртуальных машинах, а также управляете такими серверами с их помощью, вы можете использовать шаблон [iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm) или [lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app), но указания в этой статье относятся к шаблону [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database).
 - Раздел **parameters**. Здесь определены параметры, которые можно настроить для каждого ресурса. У некоторых параметров, указанных в шаблоне, есть свойство defaultValue, а у других нет. При развертывании ресурсов Azure с использованием шаблона необходимо указать значения для всех параметров, для которых в шаблоне не указано свойство defaultValue. Если не предоставить значения для параметров со свойством defaultValue, будет использоваться значение, указанное в свойстве defaultValue в шаблоне.

Шаблон определяет, какие ресурсы Azure создаются и какие параметры каждого ресурса можно настроить. Дополнительные сведения о создании собственных шаблонов см. в статье [Рекомендации по разработке шаблонов диспетчера ресурсов Azure](best-practices-resource-manager-design-templates.md).

## Скачивание и настройка существующего файла параметров

Возможно, в разных средах вы будете использовать *одни и те же* ресурсы Azure, но наверняка с *разной* конфигурацией. В этом случае вам пригодятся файлы параметров. Создайте файлы параметров, в которых будут содержаться уникальные значения для каждой среды, выполнив следующие шаги.

1. Просмотрите содержимое файла [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json), расположенного в папке 201-web-app-sql-database. Это файл параметров для файла шаблона, сохраненного при работе с предыдущим разделом.
2. В режиме просмотра нажмите кнопку [Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json).
3. Выделите содержимое этого файла с помощью мыши и сохраните его на своем компьютере в трех отдельных файлах:
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. В любом текстовом редакторе или редакторе JSON измените файл параметров среды разработки, созданный на шаге 3. Для этого замените значения, которые находятся в файле справа от значений параметров, *значениями* из следующего списка, расположенными справа от **параметров**:
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Central US*
 - **serverName**: *testapp1devsrv*
 - **serverLocation**: *Central US*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *замените своим паролем*
 - **databaseName**: *testapp1devdb*

4. В любом текстовом редакторе или редакторе JSON измените файл параметров тестовой среды, созданный на шаге 3. Для этого замените значения, которые находятся в файле справа от значений параметров, *значениями* из следующего списка, расположенными справа от **параметров**:
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Central US*
 - **serverName**: *testapp1testsrv*
 - **serverLocation**: *Central US*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *замените своим паролем*
 - **databaseName**: *testapp1testdb*

5. В любом текстовом редакторе или редакторе JSON измените файл параметров подготовительной среды, созданный на шаге 3. Замените все содержимое файла следующим кодом:

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
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

В подготовительной среде задаются другие значения, чтобы проверить, можно ли будет их использовать в рабочей среде. Эти параметры связаны с используемыми приложением [планом хостинга веб-приложений](https://azure.microsoft.com/pricing/details/app-service/) Azure, **sku** и [базой данных SQL](https://azure.microsoft.com/pricing/details/sql-database/) Azure или **requestedServiceObjectiveName**. От SKU и целей службы зависят стоимость, доступные функции, а также метрики на уровне службы.

В следующей таблице перечислены указанные в шаблоне значения по умолчанию для этих параметров, а также значения, которые используются вместо значений по умолчанию в файле параметров для подготовительной среды.

| Параметр | Значение по умолчанию | Значение в файле параметров |
|---|---|---|
| **sku** | Free | Стандартная |
| **requestedServiceObjectiveName** | S0 | S1 |

## Создание сред
Все ресурсы Azure должны создаваться в [группе ресурсов Azure](resource-group-overview.md). Группы ресурсов позволяют объединять ресурсы Azure, чтобы ими можно было управлять как единым целым. Для групп ресурсов можно назначать [разрешения](./active-directory/role-based-access-built-in-roles.md), чтобы конкретные люди в вашей организации могли создавать, изменять, удалять или просматривать их и ресурсы в них. Предупреждения и информацию о счетах за использование ресурсов в группе ресурсов можно просмотреть на [портале Azure](https://portal.azure.com). Группы ресурсов создаются в определенном [регионе](https://azure.microsoft.com/regions/) Azure. В этой статье все ресурсы создаются в одном регионе, центральной части США (Central US). При создании реальных сред вы выбираете регион, который лучше всего отвечает вашим требованиям.

Создайте группы ресурсов для каждой среды, используя любой из методов ниже. Все они приводят к одному и тому же результату.

###Интерфейс командной строки Azure

Убедитесь, что на вашем компьютере под управлением Windows, OS X или Linux [установлен](xplat-cli-install.md) интерфейс командной строки и что к подписке Azure [подключена](xplat-cli-connect.md) [учетная запись Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (которую также называют рабочей или учебной учетной записью). Чтобы создать группу ресурсов для среды разработки, введите в командной строке CLI следующую команду.

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

###PowerShell

Убедитесь, что на компьютере под управлением Windows установлена среда Azure PowerShell 1.01 или более поздней версии и что к вашей подписке подключена [учетная запись Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (которую также называют рабочей или учебной учетной записью), как описано в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md). Чтобы создать группу ресурсов для среды разработки, введите в командной строке PowerShell следующую команду.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

В случае успешного выполнения команда вернет следующий результат:

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Чтобы создать группу ресурсов для тестовой среды, введите следующую команду:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Чтобы создать группу ресурсов для подготовительной среды, введите следующую команду:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com), используя данные учетной записи [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (которую также называют рабочей или учебной учетной записью). Последовательно выберите "Создать" --> "Управление" --> "Группа ресурсов" и введите значение "TestApp1-Development" в поле "Имя группы ресурсов". Затем выберите свою подписку и в поле "Расположение группы ресурсов" укажите значение "Центральная часть США", как показано на рисунке ниже. ![Microsoft Azure](./media/solution-dev-test-environments/rgcreate.png)
2. Нажмите кнопку "Создать", чтобы создать группу ресурсов.
3. Щелкните элемент "Обзор", прокрутите список и щелкните пункт "Группы ресурсов", как показано ниже. ![Microsoft Azure](./media/solution-dev-test-environments/rgbrowse.png)
4. После этого вы увидите колонку "Группа ресурсов" с новой группой ресурсов. ![Microsoft Azure](./media/solution-dev-test-environments/rgview.png)
5. Создайте группы ресурсов TestApp1-Test и TestApp1-Pre-Production так же, как ранее создали группу TestApp1-Development.

##Развертывание ресурсов в средах

Разверните ресурсы Azure в группах ресурсов для каждой среды, используя файл шаблона для решения и файлов параметров в каждой среде. Для этого можно использовать один из методов ниже. Оба метода приводят к одному и тому же результату.

###Интерфейс командной строки Azure

В командной строке CLI введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для среды разработки. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Отобразится сообщение "Ожидание завершения развертывания", и через несколько минут команда вернет следующий код (если развертывание завершится успешно):

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
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

В случае неудачи устраните причины всех сообщений об ошибках и повторите попытку. Проблемы чаще всего связаны с использованием значений параметров, которые не соответствуют правилам именования ресурсов Azure. Другие советы по устранению неполадок см. в статье [Устранение неполадок при развертывании групп ресурсов в Azure](./resource-manager-troubleshoot-deployments-cli.md).

В командной строке CLI введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для тестовой среды. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

В командной строке CLI введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для подготовительной среды. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###PowerShell

В командной строке Azure PowerShell (1.01 или более поздней версии) введите следующую команду, чтобы развернуть ресурсы в группе ресурсов, созданной для среды разработки. При этом элемент [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Курсор начнет мерцать, и через несколько минут команда вернет следующий код (если развертывание завершится успешно):

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  В случае неудачи устраните причины всех сообщений об ошибках и повторите попытку. Проблемы чаще всего связаны с использованием значений параметров, которые не соответствуют правилам именования ресурсов Azure. Другие советы по устранению неполадок см. в статье [Устранение неполадок при развертывании групп ресурсов в Azure](./resource-manager-troubleshoot-deployments-powershell.md).

  В командной строке PowerShell введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для тестовой среды. При этом [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  В командной строке PowerShell введите следующую команду, чтобы развернуть ресурсы в группу ресурсов, созданную для подготовительной среды. При этом [path] нужно заменить на путь к файлам, сохраненным на предыдущих шагах.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Для файла шаблона и файлов параметров можно создавать версии и вести их вместе с кодом приложения в системе управления исходным кодом. Вы также можете сохранить использованные выше команды в файлы скриптов и сохранить их вместе с кодом.

> [AZURE.NOTE] Шаблон можно развернуть в Azure напрямую, нажав кнопку «Развернуть в Azure», как описано в статье [Подготовка веб-приложения с базой данных SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Указанная статья может оказаться полезной для изучения шаблонов, однако вряд ли поможет научиться изменять и сохранять шаблоны и значения параметров, а также вести учет их версий с помощью кода приложения, поэтому здесь отсутствуют сведения о дальнейших шагах этого метода.

## Поддержка сред
В процессе разработки конфигурация ресурсов Azure в одной среде может быть изменена — случайно или преднамеренно — независимо от других сред. Это может приводить к дополнительным проблемам в рамках цикла разработки приложения.

1. Измените среды. Для этого откройте [портал Azure](https://portal.azure.com).
2. Войдите в учетную запись, с помощью которой вы выполняли предыдущие шаги.
3. Выберите элементы "Обзор" --> "Группы ресурсов" (возможно, потребуется прокрутить список вниз до пункта "Группы ресурсов"), как показано на рисунке ниже. ![Microsoft Azure](./media/solution-dev-test-environments/rgbrowse.png)
4. Когда вы щелкнете "Группы ресурсов" (см. рисунок выше), отобразится колонка "Группы ресурсов" с тремя группами, созданными на предыдущем шаге, как показано на рисунке ниже. Щелкните группу ресурсов TestApp1-Development, после чего откроется колонка, в которой перечислены ресурсы, созданные на основе шаблона при развертывании группы ресурсов TestApp1-Development на предыдущем шаге. Удалите ресурс веб-приложения TestApp1DevApp. Для этого щелкните TestApp1DevApp в колонке группы ресурсов TestApp1-Development, а затем нажмите кнопку "Удалить" в колонке веб-приложения TestApp1DevApp. ![Microsoft Azure](./media/solution-dev-test-environments/portal2.png)
5. Выберите «Да», когда появится запрос на подтверждение удаления ресурса. Если закрыть колонку TestApp1-Development и снова открыть ее, в ней будет отсутствовать удаленное веб-приложение. Состав группы ресурсов теперь отличается от требуемого. Вы можете дополнительно поэкспериментировать с удалением нескольких ресурсов из разных групп ресурсов или даже с изменением параметров конфигурации некоторых ресурсов. Удалить ресурс из группы ресурсов можно не только на портале Azure, но и с помощью команды PowerShell [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) или команды "azure resource delete" в интерфейсе командной строки.
6. Чтобы вернуть все ресурсы и конфигурацию групп ресурсов в требуемое состояние, еще раз разверните среды в группах ресурсов, выполнив те же команды, которые описаны в разделе [Развертывание ресурсов в средах](#deploy-resources-to-environments), но вместо Deployment1 используйте Deployment2.
7.  Согласно рисунку к шагу 4 в колонке TestApp1-Development в разделе "Сводка" вы увидите веб-приложение, удаленное на портале на предыдущем шаге, как и другие ресурсы, которые вы могли удалить. Если вы изменили конфигурацию ресурсов, также будет видно, что в файлах параметров для них установлены предыдущие значения. Одним из преимуществ развертывания среды с помощью шаблонов диспетчера ресурсов Azure является возможность в любое время быстро повторно развернуть любое известное состояние среды.
8. Если щелкнуть значение параметра "Последнее развертывание" (см. рисунок ниже), можно увидеть колонку с историей развертывания группы ресурсов. Поскольку для первого развертывания использовалось имя Deployment1, а для второго — Deployment2, будут отображаться две записи. Если щелкнуть развертывание, отобразится колонка с результатами, которые к нему относятся. ![Microsoft Azure](./media/solution-dev-test-environments/portal3.png)



## Удаление сред
После завершения работы со средами их следует удалить, чтобы не оплачивать ресурсы Azure, которые больше не используются. Удалить среду проще, чем создать. На предыдущих шагах мы создали группы ресурсов Azure для каждой среды, а затем развернули ресурсы в эти группы.

Удалите среды одним из описанных ниже методов. Все они приводят к одному и тому же результату.

### Инфраструктура CLI Azure

Введите следующую команду в командной строке CLI:

	azure group delete "TestApp1-Development"

При появлении запроса нажмите клавишу Y, а затем ВВОД, чтобы удалить среду разработки со всеми ее ресурсами. Через несколько минут команда вернет следующий результат:

	info:    group delete command OK

Введите следующую команду в командной строке CLI, чтобы удалить остальные среды:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

В командной строке Azure PowerShell (1.01 или более поздней версии) введите следующую команду, чтобы удалить группу ресурсов и все ее содержимое.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

Если группу ресурсов точно нужно удалить, при появлении запроса нажмите клавишу Y, а затем ВВОД.

Введите следующую команду, чтобы удалить остальные среды:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Портал Azure

1. На портале Azure перейдите к группе ресурсов, как на предыдущих шагах.
2. Выберите группу TestApp1-Development, а затем в колонке группы ресурсов TestApp1-Development нажмите клавишу DELETE. Отобразится новая колонка. Введите имя группы ресурсов и нажмите кнопку "Удалить". ![Microsoft Azure](./media/solution-dev-test-environments/rgdelete.png)
3. Удалите группы ресурсов TestApp1-Test и TestApp1-Pre-Production так же, как группу TestApp1-Development.

Вне зависимости от того, какой метод используется, группы ресурсов и все их ресурсы будут удалены и вам не придется за них платить.

Чтобы свести к минимуму затраты на использование ресурсов Azure во время разработки приложения, можно использовать [службу автоматизации Azure](automation/automation-intro.md) и запланировать задания для следующих операций:

- остановка виртуальных машин в конце каждого дня и их повторный запуск в начале каждого дня;
- удаление всех сред в конце каждого дня и их повторное создание в начале каждого дня.
 
Вы узнали, как можно с легкостью создавать, поддерживать и удалять среды разработки и тестирования. Теперь можно перейти к экспериментам с описанными процедурами и значениями, а также к изучению упомянутых в этом разделе статей.

## Дальнейшие действия

- [Делегирование администрирования](./active-directory/role-based-access-control-configure.md) ресурсами в разных средах посредством назначения пользователям и группам Microsoft Azure AD определенных ролей с привилегиями на выполнения определенного набора операций с ресурсами Azure.
- [Назначение тегов](resource-group-using-tags.md) отдельным ресурсам или группам ресурсов для каждой среды. Вы можете добавить тег «Environment» для групп ресурсов и присвоить ему значение, соответствующее имени конкретной среды. Теги особенно помогут, когда требуется организовать ресурсы для выставления счетов или управления.
- Отслеживайте предупреждения и счета для ресурсов, включенных в группу ресурсов, с помощью [портала Azure](https://portal.azure.com).

<!---HONumber=AcomDC_0824_2016-->