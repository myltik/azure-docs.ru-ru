<properties
	pageTitle="Azure CLI и приложения API | Microsoft Azure"
	description="Руководство по использованию интерфейса командной строки (CLI) с приложениями API Azure для Mac, Linux и Windows."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="tdykstra"/>

# Использование интерфейса командной строки Azure (Azure CLI)

В этой статье показано, как создавать и удалять приложения API в службе приложений Azure, а также управлять ими с помощью интерфейса командной строки Azure (CLI) для Mac, Linux и Windows.

## Предварительные требования

В инструкциях этой статьи предполагается, что вы установили Azure CLI и знаете, как выполнять основные задачи. Общие сведения о CLI см. в статье [Установка и настройка CLI Azure](../xplat-cli-install.md).

> [AZURE.NOTE]Инструкции по [подключению к подписке Azure](../xplat-cli-connect.md) предлагают два варианта: вход с помощью рабочей или учебной учетной записи и загрузка файла *.publishsettings*. Проверка подлинности с помощью файла *.publishsettings* не будет работать для приложений API. Это происходит потому, что для работы с приложениями API необходимо использовать режим управления ресурсами (см. информацию о нем в следующем разделе), а файл проверки подлинности *.publishsettings* не работает с диспетчером ресурсов.

## Включение режима управления ресурсами

CLI можно использовать в режиме [управления службами (asm)](../virtual-machines/virtual-machines-command-line-tools.md) или режиме [управления ресурсами (arm)](../xplat-cli-azure-resource-manager.md). Для приложений API необходимо использовать режим управления ресурсами. Поскольку режим `arm` по умолчанию не включен, включите его с помощью команды `config mode arm`.

	azure config mode arm

## Список команд, доступных для работы с приложениями API

Чтобы просмотреть все команды, доступные для работы с приложениями API, запустите команду `apiapp`.

	azure apiapp

## Список всех приложений API в подписке или группе ресурсов

Чтобы получить список всех приложений API, доступных в вашей подписке, запустите команду `apiapp list` без параметров.

	azure apiapp list

Список содержит группу ресурсов, имя приложения API, идентификатор пакета и URL-адрес каждого приложения API.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

Чтобы ограничить список конкретной группой ресурсов, добавьте параметр группы (`-g`).

	azure apiapp list -g <resource group name>

Например:

	azure apiapp list -g mygroup

Чтобы добавить в список версию приложения API и информацию об уровне доступа, добавьте параметр сведений (`-d`).

	azure apiapp list -d

Результат `list` с дополнительными полями выглядит, как в следующем примере:

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### Список сведений о приложении API

Чтобы получить список сведений для одного приложения API, используйте команду `apiapp show` с параметрами группы (`-g`) и имени приложения API (`-n`).

	azure apiapp show -g <resource group name> -n <API app name>

Например:

	azure apiapp show -g mygroup -n SimpleDropbox

Выходные данные выглядят следующим образом:

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## Создание приложения API

Приложение API можно создать двумя способами. Вы можете использовать обязательные команды CLI, чтобы создавать ресурсы Azure по отдельности. Или используйте декларативный синтаксис в шаблоне, чтобы определить все необходимые ресурсы вместе и развернуть этот шаблон с помощью команды CLI. Информацию о декларативном подходе см. в разделе [Дальнейшие действия](#next-steps).

Чтобы создать приложение API с помощью императивного подхода, выполните следующие действия.

1. Выберите допустимое расположение.
1. Создайте или найдите группу ресурсов для использования.
2. Создайте или найдите план службы приложений для использования.
4. Создание приложения API

### Выберите допустимое расположение.

Во время создания группы ресурсов необходимо указать расположение. Далее перечислены некоторые допустимые для приложений API расположения.

* Восток США
* Запад США
* Южно-центральный регион США
* Северная Европа
* Восточная Азия
* Восточная часть Японии
* Западная Европа
* Юго-Восточная Азия
* Западная часть Японии
* Северо-центральный регион США
* Центральный регион США
* Южная часть Бразилии
* Восток США 2

Чтобы получить полный и актуальный список расположений, используйте команду `location list`. В результатах см. строку поставщика ресурсов `Microsoft.AppService/apiapps`.

	azure location list

Ниже приведен пример результатов выполнения команды `location list`.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### Создайте или найдите группу ресурсов для использования.

Чтобы создать группу ресурсов, используйте команду `group create` с параметрами имени (`n`) и расположения (`l`).

	azure group create -n <name> -l <location>

Например:

	azure group create -n "mygroup" -l "West US"

Чтобы найти существующую группу ресурсов, используйте команду `group list` и выберите группу ресурсов в расположении, допустимом для приложений API.

	azure group list

### Создайте или найдите план службы приложений для использования.

Чтобы создать план службы приложений, используйте команду `resource create` и параметр типа ресурса (`-r`), чтобы указать тип ресурса, необходимый для создания плана службы приложений.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
Например:

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

Строка JSON для параметра `properties`(`-p`) является обязательной из-за последних изменений в API REST. В будущем параметр `-p` будет необязательным.

Пример команды указывает последнюю версию API на момент написания этой статьи. Чтобы определить доступность более поздней версии, используйте команду `provider show` и см. массив `apiVersions` для объекта `sites` в массиве `resourceTypes`.

	azure provider show -n Microsoft.Web --json
   
Ниже приведен образец объекта `sites` в результатах выполнения команды.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

Чтобы получить список существующих планов службы приложений, используйте команду `resource list` и укажите план службы приложений в качестве типа ресурса с помощью параметра `-r`.

	azure resource list -r Microsoft.Web/serverfarms

Выходные данные выглядят следующим образом:

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### Создание пустого (пользовательского) приложения API

Чтобы создать пустое приложение API (в котором вы будете писать код для себя), используйте команду `apiapp create` и укажите пакет NuGet `Microsoft.ApiApp` (параметр `-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

Например:

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

Результаты отображают ход выполнения создания приложения API.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Создание приложения API из Marketplace

Чтобы получить список пакетов приложений API, доступных в Marketplace, используйте команду `group template list` и укажите приложения API с помощью категории (`-c`).

	azure group template list -c apiapps

Результаты выглядят следующим образом.

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Чтобы создать приложение API из Marketplace, выполните команду `apiapap create` и укажите имя приложения API, которое необходимо создать, используя параметр пакета NuGet (`-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

Значение, используемое для параметра `-u`, находится в средней части имени marketplace. Например, для microsoft\_com. MicrosoftSqlConnector.0.2.2 команда выглядит следующим образом:

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
Если требуется указать параметры шаблона приложения API, например имена серверов и строки подключения для соединителя SQL, появится запрос на ввод необходимых данных. Вы можете использовать `--parameters` или `--parameters-file` для передачи значений параметров. Дополнительные сведения о файлах параметров см. в разделе [Подготовка приложения API с новым шлюзом](app-service-api-arm-new-gateway-provision.md).

## Дальнейшие действия

В этой статье показано, как использовать отдельные команды Azure CLI для работы с пользовательскими приложениями API или приложениями API, созданными на основе шаблонов Marketplace. Дополнительные сведения об использовании пользовательских шаблонов для автоматизации создания приложений API см. в следующих статьях.

* [Подготовка приложения API с помощью существующего шлюза](app-service-api-arm-existing-gateway-provision.md)
* [Подготовка приложения API с помощью нового шлюза](app-service-api-arm-new-gateway-provision.md)

Дополнительные сведения об использовании служебных программ командной строки Azure с помощью диспетчера ресурсов Azure см. в статьях.
 
* [Использование интерфейса командной строки Azure для Mac, Linux и Windows со службой управления ресурсами Azure](../xplat-cli-azure-resource-manager.md).
* [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md)
 

<!---HONumber=Oct15_HO3-->