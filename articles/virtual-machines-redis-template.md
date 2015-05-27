<properties
	pageTitle="Шаблон диспетчера ресурсов Redis Cluster"
	description="Информация о том, как легко развернуть кластер Redis в виртуальных машинах Ubuntu с помощью Azure PowerShell или интерфейса командной строки Azure и шаблона диспетчера ресурсов"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# Кластер Redis с шаблоном диспетчера ресурсов 

Redis — это кэш и хранилище значений ключей с открытым кодом, в котором ключи могут содержать такие структуры данных, как строки, хэши, списки, наборы и сортируемые наборы. Redis поддерживает ряд атомарных операций с этими типами данных. В выпуске Redis версии 3.0 кластер Redis стал доступен в новейшей устойчивой версии Redis. Кластер Redis — это распределенная реализация Redis, в которой данные автоматически распределяются между различными узлами Redis, с возможностью продолжения выполнения операций в случае возникновения ошибок в подмножестве узлов. 

Кэш Microsoft Azure Redis — это выделенная служба кэша Redis, управляемая Microsoft, но не все клиенты Microsoft Azure хотят использовать кэш Redis для Azure. Некоторые предпочитают оставить свой кэш Redis за подсетью внутри собственных систем Azure, в то время как другим удобнее размещать собственные серверы Redis на виртуальных машинах Linux, чтобы воспользоваться всеми преимуществами функций Redis.

Настоящее руководство содержит информацию об использовании типового шаблона диспетчера ресурсов Azure Resource Manager (ARM) для развертывания кластера Redis на виртуальных машинах Ubuntu внутри подсети в группе ресурсов в Microsoft Azure. В дополнение к Redis 3.0 Cluster, этот шаблон поддерживает также развертывание Redis 2.8 с помощью Redis Sentinel. Следует помнить, что настоящее руководство касается только версии Redis 3.0 Cluster.

Redis Cluster создается за подсетью, поэтому доступ по общедоступному IP-адресу к Redis Cluster отсутствует. В рамках развертывания можно развернуть дополнительное «поле перехода». Такое «поле перехода» представляет собой виртуальную машину Ubuntu, также развернутую в подсети, однако оно *имеет* общедоступный IP-адрес с открытым портом SSH, к которому можно подключаться по протоколу SSH. После этого из «поля перехода» вы можете использовать SSH для всех виртуальных машин Redis в подсети.

В этом шаблоне используется концепция, принятая для выбора размеров одежды — можно выбрать «малую», «среднюю» или «большую» настройку Redis Cluster. Когда язык шаблона ARM будет поддерживать более динамичный выбор размера, его можно будет изменять для указания количества основных узлов Redis Cluster, ведомых узлов, размера виртуальных машин и т. д. Сейчас вы можете видеть размер виртуальной машины и количество основных и ведомых узлов, заданных в файле **azuredeploy.json** в переменных `tshirtSizeSmall`, `tshirtSizeMedium` и `tshirtSizeLarge`.

Шаблон Redis Cluster для «среднего» размера создает следующую конфигурацию:

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Прежде чем углубляться в дополнительную информацию, связанную с диспетчером ресурсов Azure и шаблоном, который мы использовали для этого развертывания, проверьте правильность настроек Azure PowerShell или интерфейса командной строки Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Развертывание кластера Redis на основе шаблона диспетчера ресурсов

Чтобы создать кластер Redis с использованием шаблона диспетчера ресурсов из репозитория шаблонов Github, выполните перечисленные ниже действия. Для каждого этапа приведены инструкции как для среды Azure PowerShell, так и для интерфейса командной строки Azure.

### Шаг 1-а. Загрузка файлов шаблонов с помощью PowerShell

Создайте локальную папку для JSON-файлов шаблонов и других файлов (например, C:\\Azure\\Templates\\RedisCluster).

Подставьте имя своей локальной папки и выполните следующие команды:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### Шаг 1-б. Загрузите файлы шаблонов с помощью интерфейса командной строки Azure

Вы можете клонировать репозиторий шаблона с помощью выбранного GIT-клиента, например:

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

После завершения найдите папку **redis-high-availability** в своем каталоге C:\\Azure\\Templates.

### Шаг 2 (необязательный). Изучение параметров шаблона 

При создании кластера Redis с помощью шаблона  следует указать набор параметров конфигурации. Чтобы просмотреть параметры, которые необходимо указать для шаблона в локальном JSON-файле, перед выполнением команды для создания кластера Redis, откройте JSON-файл через инструмент или текстовый редактор по своему усмотрению.

В верхней части файла найдите раздел **parameters**. Он содержит список параметров, необходимых шаблону для настройки кластера Redis. Вот как выглядит раздел **parameters** шаблона azuredeploy.json:

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "redisSubnet1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned in to"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

У каждого параметра есть такие атрибуты, как тип данных и допустимые значения. На их основе можно проверять правильность параметров, передаваемых во время выполнения шаблона в интерактивном режиме (например, через PowerShell или интерфейс командной строки Azure), а также динамически создавать пользовательские интерфейсы с возможностями обнаружения путем анализа списка обязательных параметров и их описаний.

### Шаг 3-а. Развертывание кластера Redis на основе шаблона с помощью PowerShell

Подготовьте файл параметров развертывания, создав JSON-файл со значениями времени выполнения для всех параметров. Он будет передаваться команде развертывания в качестве единого объекта. Если файл параметров не указан, PowerShell использует значения по умолчанию из шаблона и предлагает ввести те значения, которых там нет.

Ниже приведен пример списка значений, который можно найти в файле **azuredeploy-parameters.json**. Обратите внимание, что необходимо предоставить допустимые значения для параметров `storageAccountName`, `adminUsername` и `adminPassword`, а также индивидуальные настройки других параметров:

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
	},
	"subnetName": {
		"value": "Subnet1"
	},
	"addressPrefix": {
		"value": "10.0.0.0/16"
	},
	"subnetPrefix": {
		"value": "10.0.0.0/24"
	},
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]Параметр `storageAccountName` должен представлять собой несуществующее, уникальное имя учетной записи хранения, удовлетворяющее требованиям к именованию для учетной записи хранения Microsoft Azure (только буквы в нижнем регистре и цифры). Эта учетная запись хранения будет создана в рамках процесса развертывания.

Укажите имена развертывания Azure и группы ресурсов, расположение Azure и имя папки, в которой находятся сохраненные JSON-файлы. Затем выполните следующие команды:

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` должно быть уникальным в рамках вашей подписки.

При запуске команды **New-AzureResourceGroupDeployment** значения параметров извлекаются из параметров JSON-файла (azuredeploy-parameters.json) и начинается выполнение шаблона на их основе. Наличие нескольких файлов параметров для разных сред (например, тестовой, рабочей и т. д.) способствует повторному использованию шаблона и упрощает развертывание сложных решений для нескольких сред.

При развертывании следует помнить о том, что необходимо создать новую учетную запись хранения Azure. Поэтому имя, которое вы указываете в качестве параметра учетной записи хранения, должно быть уникальным и отвечать всем требованиям для такой учетной записи (содержать только буквы в нижнем регистре и цифры)

Во время развертывания экран выглядит приблизительно следующим образом:

	PS C:> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

Во время и после развертывания можно проверить все запросы, сделанные при подготовке, включая все случившиеся ошибки.

Для этого перейдите на [портал Azure](https://portal.azure.com) и выполните следующее:

- Щелкните «Обзор» на панели навигации слева, прокрутите вниз и щелкните «Группы ресурсов».
- Выберите только что созданную группу ресурсов, после чего откроется колонка «Группа ресурсов».
- В разделе «Мониторинг» выберите гистограмму «События». При этом отобразятся события для развертывания.
- Щелкнув отдельные события, вы можете просмотреть подробную информацию о каждой отдельной операции, выполненной с шаблоном.

Если после выполнения тестов вам необходимо удалить эту группу ресурсов и все ее ресурсы (учетную запись хранения, виртуальную машину и виртуальную сеть), выполните следующую команду:

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### Шаг 3-б. Развертывание кластера Redis на основе шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть кластер Redis через интерфейс командной строки Azure, сначала создайте группу ресурсов, указав для нее имя и расположение:

```powershell
azure group create TestRG "West US"
```

Передайте это имя группы ресурсов, расположение шаблона JSON-файла и расположение файла параметров (более подробно см. выше в разделе PowerShell) в следующую команду:

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

Вы можете проверить состояние развертывания отдельных ресурсов с помощью следующей команды:

```powershell
azure group deployment list TestRG
```

## Обзор структуры шаблона кластера Redis и организации файла  

Чтобы сформировать надежный подход к проектированию шаблонов диспетчера ресурсов, который можно использовать несколько раз, следует подумать над организацией ряда сложных и взаимосвязанных задач, необходимых при развертывании сложного решения, такого как Redis Cluster. Используя такие возможности ARM, как **создание связей между шаблонами**, **циклическое создание ресурсов** и выполнение сценария с помощью связанных расширений, можно реализовать модульный подход и использовать его повторно для практически любого сложного развертывания на основе шаблона.

На этой схеме представлены связи между всеми файлами, загруженными из репозитория GitHub для этого развертывания:

![redis-files](media/virtual-machines-redis-template/redis-files.png)

В этом разделе содержится описание структуры шаблона azuredeploy.json для кластера Redis.

Если вы не загрузили копию файла шаблона, укажите локальную папку для расположения файла и создайте его (например, C:\\Azure\\Templates\\RedisCluster). Введите имя папки, а затем скопируйте его и выполните следующие команды.

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Откройте шаблон azuredeploy.json в текстовом редакторе или с помощью другого выбранного средства. Ниже описана структура файла шаблона и назначение каждого раздела. Кроме того, просмотреть содержимое этого шаблона можно в браузере из [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json).

### Раздел parameters

Мы уже упоминали о роли файла **azuredeploy-parameters.json**, который будет использоваться для передачи заданного набора значений параметров во время выполнения шаблона.  В разделе parameters в azuredeploy.json указываются параметры, используемые для ввода данных в этот шаблон.

Ниже приведен пример параметра для размера:

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]Обратите внимание, что можно указать `defaultValue`, так же как `allowedValues`.

### Раздел variables

В разделе variables указываются переменные, которые могут использоваться в данном шаблоне. По сути, он содержит несколько полей (типы данных JSON или фрагменты), которым во время выполнения будут присвоены константы или вычисляемые значения. Ниже приведены некоторые примеры — от простых до более сложных:

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

 `vmStorageAccountContainerName` и `vmStorageAccountDomain` — это примеры простых переменных имя-значение. `vnetID` — это пример переменной, которая вычисляется во время выполнения с помощью функций `resourceId` и `parameters`. `machineSettings` даже более использует эту концепцию посредством вложения объекта JSON `osImageReference` в переменную `machineSettings`. `vmScripts` содержит массив JSON, `scriptsToDownload`, который вычисляется во время выполнения с помощью функций `concat` и `variables`.

Если вам необходимо настроить размер развертывания кластера Redis, можно изменить свойства переменных `tshirtSizeSmall`, `tshirtSizeMedium` и `tshirtSizeLarge` в шаблоне **azuredeploy.json**.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

Примечание: свойства `totalMemberCountExcludingLast` и `totalMemberCount` необходимы, поскольку язык шаблона в настоящий момент не содержит «математических» операций.

Дополнительные сведения о языке шаблонов можно получить на сайте MSDN [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

### Раздел resources

Именно в разделе **resources** выполняется большая часть операций. Если внимательно просмотреть этот раздел, можно незамедлительно обнаружить два различных элемента, первый из них — это элемент типа `Microsoft.Resources/deployments`, который, по сути, означает вызов вложенного развертывания в основном развертывании. Второй — это свойство `templateLink` (и связанное свойство `contentVersion`), позволяющее указывать связанный файл шаблона, который будет вызываться с набором параметров в качестве входных данных. Их можно увидеть в этом фрагменте шаблона:

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

Первый пример позволяет понять, как файл **azuredeploy.json** в этом сценарии используется для организации взаимодействия, вызывая несколько других шаблонов, каждый из которых отвечает за часть операций в ходе развертывания.

В частности, для этого развертывания будут использоваться следующие связанные шаблоны:

- **shared-resource.json** — содержит определения всех ресурсов, которые будут совместно использоваться в развертывании. Например, учетные записи хранения, используемые для хранения дисков операционной системы, виртуальных сетей и групп доступности виртуальной машины.
- **jumpbox-resources.json**: развертывает виртуальную машину «поля перехода» и все связанные ресурсы, например сетевой интерфейс, общедоступный IP-адрес и входную конечную точку для доступа к среде через SSH.
- **node-resources.json** — развертывает все виртуальные машины узла кластера Redis и подключенные ресурсы (например, сетевые карты, частные IP-адреса и т. д.). Этот шаблон также развернет расширения виртуальной машины (пользовательские сценарии для Linux) и вызовет сценарий Bash для физической установки и настройки Redis в каждом узле. Сценарий для вызова передается в этот шаблон в параметре `machineSettings`, свойство `commandToExecute`. Для всех узлов кластера Redis кроме одного развертывание и создание сценариев можно выполнять параллельно. Один узел нужно оставить напоследок, так как настройка кластера Redis может выполняться только на одном узле и должна производиться после того как на всех узлах запущен сервер Redis. Именно поэтому сценарий для выполнения передается в этот шаблон. На последнем узле должен выполняться несколько иной сценарий, который не только установит сервер Redis, но и настроит кластер Redis.

Теперь рассмотрим то, *как* используется этот последний шаблон, **node-resources.json**, так как он наиболее интересен с точки зрения разработки шаблонов. Следует обратить особое внимание на то, как один файл шаблона может развернуть несколько копий ресурсов одного типа и назначить каждому экземпляру уникальные значения для обязательных параметров. Этот принцип называется **циклическим созданием ресурсов**.

При вызове **node-resources.json** из главного файла **azuredeploy.json** он вызывается из ресурса, использующего элемент `copy` для создания своеобразного цикла. Ресурс, использующий элемент `copy`, будет «копировать» сам себя такое количество раз, которое указано в параметре `count` элемента `copy`. Для всех параметров, для которых необходимо указать уникальные значения в различных экземплярах развернутых ресурсов, затем можно использовать функцию **copyindex()**, чтобы получить числовое значение, указывающее текущий индекс в конкретном циклическом создании ресурсов. В следующем фрагменте из **azuredeploy.json** можно увидеть применение этого принципа к нескольким виртуальным машинам, создаваемым для узлов кластера Redis:

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

Другим важным принципом в создании ресурса является возможность указания для ресурсов зависимостей и приоритетов, как это можно увидеть в массиве JSON `dependsOn`. В этом отдельном шаблоне можно увидеть, что узлы кластера Redis зависят от общих ресурсов, созданных ранее.

Как было отмечено ранее, последний узел должен ожидать подготовки, пока все узлы в кластере Redis не будут обеспечены работающим на них сервером Redis. Это выполняется в **azuredeploy.json**, посредством задачи ресурса с именем `lastnode-resources`, который зависит от цикла `copy` с именем `memberNodesLoop` из приведенного выше фрагмента шаблона. После завершения `memberNodesLoop` можно подготовить `lastnode-resources`:

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

Обратите внимание на то, как ресурс `lastnode-resources` передает немного иной `machineSettings.commandToExecute` связанному шаблону. Это связано с тем, что последний узел, в дополнение к установленному серверу Redis, также требует вызова сценария для настройки кластера Redis (которая должна выполняться только один раз после включения и запуска всех серверов Redis).

Еще один интересный фрагмент, который стоит рассмотреть, связан с расширениями виртуальной машины `CustomScriptForLinux`. Они устанавливаются как отдельный тип ресурса с зависимостью от каждого узла кластера. В данном случае это используется для установки и настройки Redis на каждом узле виртуальной машины. Рассмотрим фрагмент из шаблона **node-resources.json**, в котором они используются:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

Вы увидите, что этот ресурс зависит от ресурса уже развернутой виртуальной машины (Microsoft.Compute/virtualMachines/vmMember<X>, где <X> является параметром machineSettings.machineIndex, который представляет собой индекс виртуальной машины, переданный в этот скрипт с помощью функции copyindex().

Ознакомившись с другими файлами, включенными в это развертывание, вы сможете понять все детали и рекомендации, необходимые для организации сложных стратегий развертывания решений для нескольких узлов, которые созданы на основе любой технологии, и управления ими с использованием шаблонов диспетчера ресурсов Azure. Хотя это не обязательно, мы советуем структурировать файлы шаблона с помощью подхода, указанного на следующей схеме:

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

По сути, в рамках этого подхода вы должны сделать следующее:

- Определить основной файл шаблона, который будет отвечать за организацию всех операций развертывания, вызывая на выполнение подшаблоны на основе установленных связей.
- Создать определенные файлы шаблона, которые развернут все ресурсы, используемые в остальных задачах конкретного развертывания (например, учетные записи хранения, конфигурация виртуальной сети и т. д.). Эти файлы можно повторно использовать в развертываниях со схожими требованиями в отношении общей инфраструктуры.
- Включить необязательные шаблоны ресурсов для отдельных требований конкретного заданного ресурса.
- Создать специальные шаблоны для одинаковых элементов группы ресурсов (узлы в кластере и т. д.), которые используют циклическое создание ресурсов для развертывания нескольких экземпляров с уникальными свойствами.
- Использовать расширения для развертывания сценариев и создать сценарии для каждой из технологий для всех задач, выполняемых после развертывания (например, установка продукта, конфигурация и т. д.).

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=54-->