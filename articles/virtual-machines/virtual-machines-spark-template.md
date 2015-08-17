<properties
	pageTitle="Шаблон диспетчера ресурсов для Spark под управлением Ubuntu"
	description="Узнайте, как легко развернуть кластер Spark в виртуальных машинах под управлением Ubuntu с помощью Azure PowerShell или интерфейса командной строки Azure и шаблона диспетчера ресурсов."
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# Использование Spark в ОС Ubuntu с шаблоном диспетчера ресурсов

Apache Spark — это среда, позволяющая быстро обрабатывать большие объемы данных. В ней предусмотрен расширенный механизм DAG, поддерживающий циклическую обработку потоков данных и вычисления в оперативной памяти, а также предоставляет доступ к различным источникам данных, включая HDFS, Spark, HBase и S3.

Spark работает под управлением диспетчеров кластеров Mesos или YARN и предоставляет простой режим автономного развертывания. В этом учебнике приводится пошаговое руководство по использованию примера шаблона диспетчера ресурсов Azure для развертывания кластера Spark в виртуальных машинах под управлением Ubuntu с помощью [Azure PowerShell](../powershell-install-configure.md) или [Azure CLI](../xplat-cli.md).

Этот шаблон позволяет развернуть кластер Spark в виртуальных машинах под управлением Ubuntu и предусматривает подготовку учетной записи хранения, виртуальной сети, групп доступности, общедоступных IP-адресов и сетевых интерфейсов, которые необходимы для установки. Кластер Spark создается за подсетью, поэтому доступ к нему по общедоступному IP-адресу отсутствует. В рамках развертывания можно создать дополнительное «поле перехода». Оно представляет собой виртуальную машину Ubuntu, также развернутую в подсети, у которой, однако, *есть* общедоступный IP-адрес с открытым SSH-портом, к которому можно подключаться. Используя «поле перехода» вы можете подключаться ко всем виртуальным машинам Spark в подсети по протоколу SSH.

В этом шаблоне используется концепция, принятая для обозначения размеров одежды: вы можете выбрать «малый» (S), «средний» (M) или «большой» (L) размер кластера Spark. Когда язык шаблона будет поддерживать более динамичный выбор размера, его можно будет изменять для указания количества основных узлов кластера Spark, подчиненных узлов, размера виртуальных машин и т. д. А пока вы можете видеть размер виртуальной машины и количество основных и подчиненных узлов, заданных в переменных **tshirtSizeS**, **tshirtSizeM** и **tshirtSizeL** в файле azuredeploy.json:

- S — 1 главный, 1 подчиненный
- M — 1 главный, 4 подчиненных
- L — 1 главный, 6 подчиненных

Топология новых кластеров, развертываемых на базе этого шаблона, представлена на следующей схеме, однако вы можете легко создавать и другие топологии, адаптировав с учетом своих требований шаблон, описанный в этой статье:

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

Как показано на приведенном выше рисунке, топология развертывания состоит из следующих элементов:

-	Новая учетная запись хранения, в которой размещается диск с ОС для вновь созданных виртуальных машин.
-	Виртуальная сеть с подсетью. Все виртуальные машины, создаваемые с помощью шаблона, подготавливаются внутри этой виртуальной сети.
-	Группа доступности для главного и подчиненного узлов.
-	Основной узел во вновь созданной группе доступности.
-	Четыре подчиненных узла в той же виртуальной подсети и группе доступности, что и основной узел.
-	Виртуальная машина в той же виртуальной сети и подсети, выполняющая функцию «поля перехода» и позволяющая обращаться к кластеру.

По умолчанию используется версия Spark 3.0.0. Ее можно изменить на любые готовые скомпилированные двоичные файлы в репозитории Spark. В сценарии также предусмотрено требование раскомментировать сборку из исходного кода. Каждому главному узлу Spark назначается статический IP-адрес: 10.0.0.10. Каждому ведомому узлу будет назначен статический IP-адрес. Это позволит обойти текущее ограничение, из-за которого невозможно динамически составить список IP-адресов из шаблона. (По умолчанию первому узлу будет назначен частный IP-адрес 10.0.0.30, второму узлу — 10.0.0.31 и т. д.) Чтобы просмотреть ошибки развертывания, перейдите на новый портал Azure и найдите раздел **Группы ресурсов** > **Последнее развертывание** > **Проверить сведения об операции**.

Прежде чем углубляться в дополнительную информацию, связанную с диспетчером ресурсов Azure и шаблоном, который мы использовали для этого развертывания, проверьте правильность настроек Azure PowerShell или интерфейса командной строки Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Создание кластера Spark с помощью шаблона диспетчера ресурсов

Чтобы создать кластер Spark с помощью шаблона диспетчера ресурсов из репозитория шаблонов Github, выполните перечисленные ниже действия в Azure PowerShell или Azure CLI.

### Шаг 1-а. Скачивание файлов шаблонов с помощью Azure PowerShell

Создайте локальную папку для JSON-файлов шаблонов и других файлов (например, C:\\Azure\\Templates\\Spark).

Подставьте имя своей локальной папки и выполните следующие команды:

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### Шаг 1-б. Скачивание файлов шаблонов с помощью интерфейса командной строки Azure

Вы можете клонировать репозиторий шаблона с помощью выбранного клиента GIT, например:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

После завершения найдите папку **spark-on-ubuntu** в каталоге C:\\Azure\\Templates.

### Шаг 2 (необязательный). Изучение параметров шаблона

При создании кластера Spark с помощью шаблона необходимо задать ряд необходимых параметров конфигурации. Объявив эти параметры в определении шаблона, вы сможете указать значения во внешнем файле или в командной строке в процессе развертывания.

В разделе parameters в верхней части файла azuredeploy.json есть набор параметров, необходимых для настройки кластера Spark с помощью шаблона. Вот как может выглядеть раздел parameters файла azuredeploy.json из этого шаблона:

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
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
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
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

У каждого параметра есть такие атрибуты, как тип данных и допустимые значения. На их основе можно проверять правильность параметров, передаваемых во время выполнения шаблона в интерактивном режиме (например, через Azure PowerShell или интерфейс командной строки Azure), а также динамически создавать пользовательские интерфейсы с возможностями обнаружения путем анализа списка обязательных параметров и их описаний.

### Шаг 3-а. Развертывание кластера Spark на основе шаблона с помощью Azure PowerShell

Подготовьте файл параметров развертывания, создав JSON-файл с рабочими значениями всех параметров. Он будет передаваться команде развертывания в качестве единого объекта. Если файл параметров не указан, Azure PowerShell использует значения по умолчанию из шаблона и предлагает ввести те значения, которых там нет.

Вот пример набора параметров из файла azuredeploy-parameters.json. Обратите внимание, что необходимо указать допустимые значения для параметров **storageAccountName**, **adminUsername** и **adminPassword**, а также при необходимости изменить другие параметры:

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
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
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Укажите имена развертывания Azure и группы ресурсов, расположение Azure и имя папки, в которой находится сохраненный JSON-файл. Затем выполните следующие команды:

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]Значение **$RGName** должно быть уникальным в пределах вашей подписки.

При запуске команды **New-AzureResourceGroupDeployment** значения параметров извлекаются из JSON-файла и начинается выполнение шаблона на их основе. Использование нескольких файлов параметров в разных средах (например, тестовой, рабочей и т. д.) способствует повторному использованию шаблона и упрощает развертывание сложных решений для нескольких сред.

При развертывании следует помнить о том, что необходимо создать новую учетную запись хранения Azure. Поэтому имя, которое вы указываете в качестве параметра учетной записи хранения, должно быть уникальным и отвечать всем требованиям для такой учетной записи (содержать только буквы в нижнем регистре и цифры).

Во время развертывания экран выглядит приблизительно следующим образом:

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

Во время и после развертывания можно проверить все запросы, сделанные на этапе подготовки, включая все возникшие ошибки.

Для этого перейдите на [портал Azure](https://portal.azure.com) и сделайте следующее:

- Щелкните **Обзор** на панели навигации слева, прокрутите вниз и щелкните **Группы ресурсов**.
- Щелкните только что созданную группу ресурсов, после чего откроется колонка «Группа ресурсов».
- Щелкнув гистограмму **События** в области **Мониторинг** колонки «Группа ресурсов», вы увидите события для своего развертывания.
- Щелкнув отдельные события, вы можете просмотреть подробную информацию о каждой отдельной операции, выполненной с шаблоном.

![portal-events](media/virtual-machines-spark-template/portal-events.png)

Если после проверки вам потребуется удалить эту группу и все ее ресурсы (учетную запись хранения, виртуальную машину и виртуальную сеть), выполните следующую команду:

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### Шаг 3-б. Развертывание кластера Spark на основе шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть кластер Spark с помощью интерфейса командной строки Azure, создайте группу ресурсов, указав для нее имя и расположение:

	azure group create SparkResourceGroup "West US"

Передайте это имя группы ресурсов, расположение шаблона JSON-файла и расположение файла параметров в следующую команду (дополнительную информацию см. в разделе для Azure PowerShell выше):

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Проверить состояние развертывания отдельных ресурсов можно с помощью следующей команды:

	azure group deployment list SparkResourceGroup

## Обзор структуры шаблона и организации файлов Spark

Чтобы создать функциональный шаблон диспетчера ресурсов, который можно будет использовать многократно, следует продумать выполнение нескольких сложных и взаимосвязанных задач, необходимых при развертывании комплексного решения, такого как Spark. Используя такие функциональные возможности диспетчера ресурсов, как связывание шаблонов, циклическое создание ресурсов и запуск сценариев с помощью связанных расширений, можно реализовать модульный подход и использовать его практически для любого сложного развертывания на основе шаблона.

На этой схеме представлены связи между всеми файлами, загруженными из репозитория GitHub для этого развертывания:

![spark-files](media/virtual-machines-spark-template/spark-files.png)

В этом разделе содержится описание структуры файла azuredeploy.json для кластера Spark.

Если вы не скачали копию файла шаблона, укажите локальную папку, куда он будет помещен (например, C:\\Azure\\Templates\\Spark), и создайте его. Введите имя папки, а затем скопируйте его и выполните следующие команды:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Откройте шаблон azuredeploy.json в текстовом редакторе или с помощью другого выбранного средства. Ниже описана структура файла шаблона и назначение каждого раздела. Кроме того, просмотреть содержимое этого шаблона можно в браузере из [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json).

### Раздел parameters

В разделе parameters файла azuredeploy.json задаются параметры, используемые в шаблоне. Значения передаются в раздел parameters файла azuredeploy.json во время выполнения шаблона с помощью вышеупомянутого файла azuredeploy-parameters.json.

Вот пример параметра в виде «размера»:

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]Обратите внимание, что можно указать как **defaultValue**, так и **allowedValues**.

### Раздел variables

В разделе variables указываются переменные, которые могут использоваться в данном шаблоне. Он содержит несколько полей (типы данных JSON или фрагменты), которым во время выполнения будут присвоены константы или вычисляемые значения. Ниже приведены некоторые примеры — от простых до более сложных:

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

**vmStorageAccountContainerName** — это пример переменной «имя-значение». **vnetID** — это пример переменной, которая вычисляется во время выполнения с помощью функций **resourceId** и **parameters**. Значения переменных **numberOfMasterInstances** и **vmSize** вычисляются во время выполнения с помощью функций **concat**, **variables** и **parameters**.

Если необходимо настроить размер кластера Spark, измените свойства переменных **tshirtSizeS**, **tshirtSizeM** и **tshirtSizeL** в шаблоне azuredeploy.json.

Дополнительные сведения о языке шаблонов см. в статье [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx) на сайте MSDN.


### Раздел resources

Именно в разделе resources выполняется большая часть операций. Если тщательно изучить этот раздел, можно сразу определить два разных элемента. Первый из них — это элемент типа `Microsoft.Resources/deployments`, который по сути вызывает вложенное развертывание внутри основного. Второй — это свойство **templateLink** (и связанное свойство **contentVersion**), позволяющее указывать связанный файл шаблона, который будет вызываться с набором параметров в качестве входных данных. Их можно увидеть в этом фрагменте шаблона:

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

Первый пример позволяет понять, как файл azuredeploy.json в этом сценарии используется для организации взаимодействия, вызывая несколько других шаблонов. Каждый файл отвечает за часть необходимых этапов развертывания.

В частности, для этого развертывания будут использоваться следующие связанные шаблоны:

-	**shared-resource.json**: содержит определения всех ресурсов, которые будут совместно использоваться в развертывании. К ним относятся, например, учетные записи хранения, используемые для хранения дисков ОС и виртуальных сетей для виртуальной машины.
-	**jumpbox-resources-enabled.json**: развертывает «поле перехода» и все связанные с этой виртуальной машиной ресурсы, например сетевой интерфейс, общедоступный IP-адрес и входную конечную точку для доступа к среде через SSH.

После вызова этих двух шаблонов azuredeploy.json подготавливает все виртуальные машины кластера Spark и связанные с ними ресурсы (сетевые адаптеры, частные IP-адреса и т. д.). Этот шаблон также развернет расширения для виртуальной машины (пользовательские сценарии для Linux) и вызовет сценарий Bash (spark-cluster-install.sh) для физической установки и настройки Spark на каждом узле.

Теперь рассмотрим, *как* используется последний шаблон, azuredeploy.json, так как он наиболее интересен с точки зрения разработки шаблонов. Следует обратить особое внимание на то, как один файл шаблона может развернуть несколько копий ресурсов одного типа и назначить каждому экземпляру уникальные значения для обязательных параметров. Этот принцип называется **циклическим созданием ресурсов**.

Ресурс, использующий элемент **copy**, будет «копировать» сам себя такое количество раз, которое указано в параметре **count **элемента **copy**. Для всех параметров, для которых необходимо указать уникальные значения в различных экземплярах развернутых ресурсов, затем можно использовать функцию **copyindex()**, чтобы получить числовое значение, указывающее текущий индекс в конкретном циклическом создании ресурсов. В следующем фрагменте azuredeploy.json можно увидеть применение этого принципа к нескольким сетевым адаптерам, виртуальным машинам и расширениям для них, создаваемым для кластера Spark:

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

Другим важным принципом в создании ресурса является возможность указать зависимости и приоритеты для ресурсов, как можно увидеть в массиве JSON **dependsOn**. В этом конкретном шаблоне можно увидеть, что узлы кластера Spark зависят от созданных ранее общих ресурсов и ресурсов **networkInterfaces**.

Еще один интересный фрагмент, который стоит рассмотреть, связан с расширениями виртуальной машины **CustomScriptForLinux**. Они устанавливаются как отдельный тип ресурса с зависимостью от каждого узла кластера. В данном случае они применяются для установки и настройки Spark на каждом узле виртуальной машины. Рассмотрим фрагмент из шаблона azuredeploy.json, в котором они используются:

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

Обратите внимание, что расширения для ресурсов главного и подчиненного узлов выполняют в процессе подготовки разные команды, определенные в свойстве **commandToExecute**.

Если взглянуть на фрагмент кода JSON последнего расширения виртуальной машины, можно увидеть, что этот ресурс зависит от ресурса виртуальной машины и ее сетевого интерфейса. Это означает, что эти два ресурса уже должны быть развернуты перед подготовкой и запуском этого расширения виртуальной машины. Также обратите внимание на использование функции **copyindex()** для повторения этого шага для каждой ведомой виртуальной машины.

Ознакомившись с другими файлами в этом развертывании, можно понять, какие принципы и методы используются для организации сложных развертываний решений для нескольких узлов на базе любой технологии и управления ими с помощью шаблонов диспетчера ресурсов Azure. Хотя это не обязательно, мы советуем структурировать файлы шаблона с помощью подхода, указанного на следующей схеме:

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

По сути, в рамках этого подхода необходимо сделать следующее:

-	определить основной файл шаблона в качестве центральной точки оркестрации для всех определенных операций развертывания за счет использования создания связей между шаблонами для вызова операций выполнения подшаблона;
-	создать определенные файлы шаблона, которые развернут все ресурсы, используемые в остальных задачах конкретного развертывания (учетные записи хранения, конфигурация виртуальной сети и т. д.). Эти файлы можно повторно использовать в развертываниях со схожими требованиями в отношении общей инфраструктуры.
-	включить необязательные шаблоны ресурсов для отдельных требований конкретного заданного ресурса;
-	создать специальные шаблоны для одинаковых элементов группы ресурсов (узлы в кластере и т. д.), которые используют циклическое создание ресурсов для развертывания нескольких экземпляров с уникальными свойствами;
-	использовать расширения для развертывания сценариев и создать сценарии для каждой из технологий для всех задач, выполняемых после развертывания (установка продукта, конфигурация и т. д.).

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Дальнейшие действия

Ознакомьтесь с дополнительной информацией о [развертывании шаблона](../resource-group-template-deploy.md).

Изучите другие [платформы приложений](virtual-machines-app-frameworks.md).

[Устраняйте неполадки развертывания шаблонов](resource-group-deploy-debug.md).

<!---HONumber=August15_HO6-->