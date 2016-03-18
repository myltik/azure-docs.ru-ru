<properties
	pageTitle="Служба автоматизации DocumentDB — диспетчер ресурсов — CLI | Microsoft Azure"
	description="Разверните учетную запись базы данных DocumentDB с помощью шаблонов диспетчера ресурсов Azure или интерфейса командной строки. DocumentDB — это облачная база данных NoSQL для данных JSON."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="mimig"/>

# Автоматизация создания учетной записи DocumentDB с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

В этой статье показано, как создать учетную запись DocumentDB с помощью шаблонов диспетчера ресурсов Azure или интерфейса командной строки (CLI Azure). Информацию о создании учетной записи DocumentDB с помощью портала Azure см. в статье [Создание учетной записи базы данных DocumentDB](documentdb-create-account.md).

- [Создание учетной записи DocumentDB с помощью интерфейса командной строки](#quick-create-documentdb-account)
- [Создание учетной записи DocumentDB с помощью шаблона ARM](#deploy-documentdb-from-a-template)

Учетные записи базы данных DocumentDB в настоящее время являются единственным ресурсом DocumentDB, который можно создать с помощью шаблонов ARM и Azure CLI.

## Подготовка

Чтобы использовать интерфейс командной строки Azure с группами ресурсов Azure, вам понадобится подходящая версия интерфейса командной строки Azure, а также учетная запись Azure. Если у вас нет интерфейса командной строки Azure, [установите его](../xplat-cli-install.md).

### Обновление версии интерфейса командной строки Azure

В командной строке введите команду `azure --version`, чтобы узнать, установлена ли у вас версия 0.9.11 или более поздняя.

	azure --version
    0.9.11 (node: 0.12.7)

Если у вас не установлена версия 0.9.11 или более поздняя, [установите интерфейс командной строки Azure](../xplat-cli-install.md) либо выполните обновление c помощью одного из встроенных установщиков или команды **npm** (введите `npm update -g azure-cli` для обновления или `npm install -g azure-cli` для установки).

### Настройка учетной записи и подписки Azure

Если у вас нет подписки Azure, но есть подписка Visual Studio, вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или подписаться на [бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/).

Чтобы использовать шаблоны управления ресурсами Azure, требуется рабочая или учебная учетная запись или учетная запись Майкрософт. Если у вас есть одна из этих учетных записей, введите следующую команду.

	azure login

В результате будут возвращены следующие выходные данные:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate. If you're signing in as an Azure
    AD application, use the --username and --password parameters.

> [AZURE.NOTE] Если у вас нет учетной записи Azure, появится сообщение об ошибке, указывающее, что вам необходима учетная запись другого типа. Сведения о том, как создать такую учетную запись из текущей учетной записи Azure, см. в статье [Создание рабочего или школьного удостоверения в Azure Active Directory](../virtual-machines/resource-group-create-work-id-from-personal.md).

Откройте адрес [https://aka.ms/devicelogin](https://aka.ms/devicelogin) в браузере и введите код, указанный в выходных данных команды.

![Снимок экрана, демонстрирующий вход устройства в систему для командной строки Microsoft Azure.](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

После введения кода выберите удостоверение, которое вы хотите использовать в браузер, и при необходимости укажите имя пользователя и пароль.

![Снимок экрана, показывающий, где можно выбрать удостоверение учетной записи Майкрософт, связанной с подпиской Azure, которую вы хотите использовать.](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Если вход успешно выполнен, появится следующий экран подтверждения, после чего вы можете закрыть окно браузера.

![Снимок экрана, показывающий подтверждение входа в кроссплатформенный интерфейс командной строки Microsoft Azure.](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

Кроме того, командная оболочка предоставляет следующие выходные данные.

    -info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Помимо описанного здесь метода интерактивного входа в систему, доступны дополнительные методы входа Azure CLI. Дополнительные сведения о других методах и информацию о работе с нескольким подписками см. в разделе [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md).

### Переключение в режим группы ресурсов в интерфейсе командной строки Azure

По умолчанию интерфейс командной строки Azure запускается в режиме управления службой (режим **asm**). Чтобы перейти в режим группы ресурсов, введите следующее.

	azure config mode arm

В результате будут возвращены такие выходные данные:

	info:    New mode is arm

Вы можете переключиться к набору команд по умолчанию, введя `azure config mode asm`.

## <a id="quick-create-documentdb-account"></a>Задача: создание учетной записи DocumentDB с помощью Azure CLI

Используйте указания в этом разделе, чтобы создать учетную запись DocumentDB с помощью интерфейса командной строки Azure.

### Шаг 1. Создание или получение группы ресурсов

Чтобы создать учетную запись DocumentDB, сначала необходима группа ресурсов. Если известно имя группы ресурсов, которую вы хотите использовать, перейдите к [шагу 2](#create-documentdb-account-cli).

Чтобы просмотреть список всех текущих групп ресурсов, выполните следующую команду и запомните имя группы ресурсов, которую вы хотите использовать:

    azure group list

Чтобы создать новую группу ресурсов, выполните следующую команду, укажите имя новой группы ресурсов для создания и регион, в котором необходимо создать группу ресурсов:

	azure group create <resourcegroupname> <resourcegrouplocation>

 - В параметре `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой. 
 - В параметре `<resourcegrouplocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

Пример входных данных:

	azure group create new_res_group westus

В результате будут возвращены следующие выходные данные:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).

### <a id="create-documentdb-account-cli"></a>Шаг 2. Создание учетной записи DocumentDB с помощью CLI

Создайте учетную запись DocumentDB в новой или существующей группе ресурсов, введя следующую команду в командной строке:

> [AZURE.TIP] При выполнении этой команды в Azure PowerShell или Windows PowerShell вы получите ошибку о непредвиденном токене. Вместо этого запустите эту команду в командной строке Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

 - В параметре `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой. 
 - В параметре `<databaseaccountname>` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов.
 - В параметре `<databaseaccountlocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

Пример входных данных:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

В результате будут возвращены следующие выходные данные после подготовки вашей новой учетной записи:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## <a id="deploy-documentdb-from-a-template"></a>Задача: создание учетной записи DocumentDB с помощью шаблона ARM

Используйте инструкции в этом разделе для создания учетной записи DocumentDB с помощью шаблона диспетчера ресурсов Azure (ARM) и файла необязательных параметров, каждый из которых является JSON-файлом. Использование шаблона позволяет точно описать, что вы хотите, и повторить все без ошибок.

### Общая информация о шаблонах ARM и группах ресурсов

Большинство приложений создаются с использованием различных ресурсов. Это может быть комбинация из одной или нескольких учетных записей DocumentDB и учетных записей хранения, виртуальной сети или сети доставки содержимого. API управления службами Azure по умолчанию и портал Azure представляют эти компоненты в рамках подхода «одна служба за другой». Такой подход предполагает отдельное развертывание каждой службы и управление ею (или использование других инструментов для выполнения этой задачи) вместо обработки единой логической единицы развертывания.

*Шаблоны диспетчера ресурсов Azure* позволяют развертывать различные ресурсы и управлять ими как единой логической единицей развертывания с помощью декларативного подхода. Вместо того чтобы прямо сообщать Azure о том, что необходимо развертывать, выполняя одну команду за другой, вы просто описываете все развертывание в JSON-файле, т. е. все ресурсы и соответствующие параметры конфигурации и развертывания, и сообщаете Azure о том, что эти ресурсы требуется развернуть как одну группу.

Дополнительную информацию о группах ресурсов Azure и их возможностях см. в статье [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md). Если вас интересует разработка шаблонов, ознакомьтесь со статьей [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

### Шаг 1. Создание файла шаблона и параметров

Создайте локальный файл шаблона со следующим содержимым: Имя файла azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

Этот шаблон требует только одного параметра — имени учетной записи базы данных для создания. Параметр задает в качестве расположения учетной записи базы данных то же расположение, что и у группы ресурсов.

Поскольку шаблон принимает только один параметр, вы можете ввести значение в командной строке или создать файл параметров для указания значения.

Чтобы создать файл параметров, скопируйте следующее содержимое в новый файл и назовите файл azuredeploy.parameters.json. Если планируется указать имя учетной записи базы данных в командной строке, вы можете продолжить работу без создания этого файла.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

В файле azuredeploy.parameters.json измените значение samplearmacct на имя базы данных, которую вы хотите использовать, а затем сохраните файл. В параметре `<databaseAccountName>` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов.

### Шаг 2. Создание или получение группы ресурсов

Чтобы создать учетную запись DocumentDB, сначала необходима группа ресурсов. Если вы уже знаете нужное имя группы ресурсов, убедитесь, что в качестве расположения выбран [регион, где служба DocumentDB является общедоступной](https://azure.microsoft.com/regions/#services), и перейдите к [шагу 3](#create-account-from-template). При использовании шаблона учетная запись создается в том же регионе, что и группа ресурсов, поэтому попытка создать учетную запись в регионе, в котором нет доступа к DocumentDB, приведет к ошибке развертывания.

Чтобы просмотреть список всех текущих групп ресурсов, выполните следующую команду и запомните имя группы ресурсов, которую вы хотите использовать:

    azure group list

Чтобы создать новую группу ресурсов, выполните следующую команду, укажите имя новой группы ресурсов для создания и регион, в котором необходимо создать группу ресурсов:

	azure group create <resourcegroupname> <databaseaccountlocation>

 - В параметре `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой. 
 - В параметре `<databaseaccountlocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

Пример входных данных:

	azure group create new_res_group westus

В результате будут возвращены следующие выходные данные:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

При возникновении ошибок см. раздел [Устранение неисправностей](#troubleshooting).

### <a id="create-account-from-template"></a>Шаг 3. Создание учетной записи DocumentDB с помощью шаблона ARM

Чтобы создать учетную запись DocumentDB в группе ресурсов, выполните следующую команду и укажите путь к файлу шаблона, путь к файлу параметра или значение параметра, имя группы ресурсов, в которой будет проводиться развертывание, и имя развертывания (-n является необязательным).

Использование файла параметров:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` — путь к файлу azuredeploy.json, созданному на шаге 1.
 - `<PathToParameterFile>` — путь к файлу azuredeploy.parameters.json, созданному на шаге 1.
 - `<resourcegroupname>` — имя существующей группы ресурсов, в которую добавляется учетная запись базы данных DocumentDB. 
 - `<deploymentname>` — необязательное имя развертывания.

Пример входных данных:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

ИЛИ, чтобы указать параметр имени учетной записи базы данных без файла параметров и получить значение, выполните следующую команду:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Вот пример, в котором показаны командная строка и запись для учетной записи базы данных с именем new\_db\_acct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

По мере подготовки учетной записи вы получите следующие сведения:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    location             String  West US
    info:    group deployment create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## Устранение неполадок

Существует несколько вариантов устранения ошибок типа `Deployment provisioning state was not successful`, возникающих при создании группы ресурсов или учетной записи базы данных.

> [AZURE.NOTE] Указание недопустимых символов в имени учетной записи базы данных или выбор региона, в котором отсутствует доступ к DocumentDB, приводит к ошибкам развертывания. В именах учетных записей могут использоваться только строчные буквы, цифры и символ «-», а длина имени должна составлять от 3 до 50 символов. Все допустимые расположения учетной записи базы данных перечислены на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

- Если выходные данные содержат ошибку `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, информацию об этой ошибке можно просмотреть в файле azure.err.

- Полезные сведения также можно найти в файле журнала группы ресурсов. Чтобы просмотреть файл журнала, выполните следующую команду:

    	azure group log show <resourcegroupname> --last-deployment

    Пример входных данных:

    	azure group log show new_res_group --last-deployment

    Для получения дополнительных сведений см. статью [Устранение неполадок при развертывании групп ресурсов в Azure](../virtual-machines/resource-group-deploy-debug.md).

- Кроме того, сведения об ошибках доступны на портале Azure, как показано на следующем снимке экрана. Чтобы перейти к информации об ошибке: нажмите "Группа ресурсов" на навигационной панели, выберите группу ресурсов, в которой есть ошибка, затем в области Essentials в колонке группы ресурсов щелкните дату последнего развертывания, выберите неудачное развертывание в колонке журналов развертываний и щелкните сведения об операции с красным восклицательным знаком в колонке развертывания. В колонке сведений об операции отображается сообщение о состоянии неудачного развертывания.

    ![Снимок экрана портала Azure, демонстрирующий, как перейти к сообщению об ошибке развертывания](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## Дальнейшие действия

Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

- портал Azure, как описано в статье [Как создать базу данных для DocumentDB](documentdb-create-database.md);
- примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на портале GitHub;
- [Пакеты SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. 

После создания базы данных необходимо [добавить одну или несколько коллекций](documentdb-create-collection.md) в базе данных, а затем [добавить документы](documentdb-view-json-document-explorer.md) в коллекции.

После добавления документов в коллекции можно использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале предварительной версии, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Дополнительные сведения о DocumentDB см. в следующих разделах:

-	[Схема обучения для DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Модель ресурсов и понятия DocumentDB](documentdb-resources.md)

Дополнительные шаблоны см. на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_0128_2016-->