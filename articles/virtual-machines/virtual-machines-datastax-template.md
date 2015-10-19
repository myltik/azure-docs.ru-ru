<properties
	pageTitle="Использование решения DataStax в ОС Ubuntu с помощью шаблона диспетчера ресурсов | Microsoft Azure"
	description="Узнайте, как легко развернуть кластер DataStax в виртуальных машинах Ubuntu с помощью Azure PowerShell или интерфейса командной строки Azure и диспетчера ресурсов шаблона"
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# Использование решения DataStax в ОС Ubuntu с помощью шаблона диспетчера ресурсов

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурса с помощью модели развертывания на основе диспетчера ресурсов.

Компания DataStax — признанный лидер в отрасли разработки и поставок решений на базе Apache Cassandra. Это предназначенная для предприятий технология, которая позволяет создавать корпоративные распределенные базы данных NoSQL. Эта технология заслужила широкое признание как гибкая, надежная (работает непрерывно) и предсказуемо масштабируемая до любого размера. DataStax предлагает варианты Enterprise (DSE) и Community (DSC). Кроме того, имеются возможности для выполнения вычислений в оперативной памяти, обеспечения безопасности в масштабе предприятия, использования быстрых и мощных интегрированных средств аналитики и поиска в корпоративной среде.

В дополнение к возможностям, доступным в Azure Marketplace, теперь вы можете легко развернуть новый кластер DataStax на виртуальных машинах Ubuntu с помощью шаблона диспетчера ресурсов, развернутого с использованием [Azure PowerShell](../powershell-install-configure.md) или [интерфейса командной строки Azure](../xplat-cli-install.md).

Топология новых кластеров, развертываемых на базе этого шаблона, представлена на следующей схеме, однако вы можете легко создавать и другие топологии, адаптировав с учетом своих требований шаблон, описанный в этой статье:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

С помощью параметров можно задать количество узлов, которые будут развернуты в новом кластере Apache Cassandra. Экземпляр службы DataStax Operation Center будет развернут также на отдельной виртуальной машине в той же виртуальной сети, что даст вам возможность следить за состоянием кластера и всех отдельных узлов, добавлять или удалять узлы и выполнять все административные задачи, связанные с этим кластером.

После завершения развертывания вы можете получить доступ к экземпляру виртуальной машины DataStax Operations Center с помощью заданного DNS-адреса. В виртуальной машине OpsCenter включен SSH-порт 22 и HTTPS-порт 8443. DNS-адрес для Operations Center будет включать параметры *dnsName* и *region* в формате `{dnsName}.{region}.cloudapp.azure.com`. Если вы создали развертывание и указали для параметра *dnsName* значение datastax в регионе «Запад США», вы можете получить доступ к виртуальной машине DataStax Operations Center для развертывания по адресу `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]Сертификат, используемый в развертывании, является самозаверяющим сертификатом, который создаст предупреждение в браузере. Чтобы заменить этот сертификат собственным SSL-сертификатом, выполните процедуры, указанные на веб-сайте [DataStax](http://www.datastax.com/).

Прежде чем углубляться в дополнительную информацию, связанную с диспетчером ресурсов Azure и шаблоном, который мы использовали для этого развертывания, проверьте правильность настроек Azure PowerShell или интерфейса командной строки Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Создание кластера Cassandra на основе DataStax с помощью шаблона диспетчера ресурсов

Выполните указанные ниже действия, чтобы создать кластер Apache Cassandra на основе DataStax с помощью шаблона диспетчера ресурсов из репозитория шаблонов GitHub. Для каждого этапа приведены инструкции как для среды Azure PowerShell, так и для интерфейса командной строки Azure.

### Шаг 1-а. Скачивание файлов шаблонов с помощью Azure PowerShell

Создайте локальную папку для JSON-файлов шаблонов и других файлов (например, C:\\Azure\\Templates\\DataStax).

Подставьте имя своей локальной папки и выполните следующие команды:

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Шаг 1-б. Скачивание файлов шаблонов с помощью интерфейса командной строки Azure

Вы можете клонировать репозиторий шаблона с помощью выбранного клиента GIT, например:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

После завершения найдите папку **datastax-on-ubuntu** в каталоге C:\\Azure\\Templates.

### Шаг 2 (необязательный). Изучение параметров шаблона

При развертывании нестандартных решений, таких как кластер Apache Cassandra на основе DataStax, необходимо указать набор параметров конфигурации для работы с рядом необходимых параметров. Объявив эти параметры в определении шаблона, во время развертывания можно указать их значения во внешнем файле или в командной строке.

В разделе parameters в верхней части файла azuredeploy.json вы найдете набор параметров, которые требуются шаблону для настройки кластера DataStax. Вот как может выглядеть раздел parameters файла azuredeploy.json из этого шаблона:

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the operations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
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
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

У каждого параметра есть такие атрибуты, как тип данных и допустимые значения. На их основе можно проверять правильность параметров, передаваемых во время выполнения шаблона в интерактивном режиме (например, через Azure PowerShell или интерфейс командной строки Azure), а также динамически создавать пользовательские интерфейсы с возможностями обнаружения путем анализа списка обязательных параметров и их описаний.

### Шаг 3-а. Развертывание кластера DataStax на основе шаблона с помощью Azure PowerShell

Подготовьте файл параметров развертывания, создав JSON-файл с рабочими значениями всех параметров. Он будет передаваться команде развертывания в качестве единого объекта. Если файл параметров не указан, Azure PowerShell использует значения по умолчанию из шаблона и предлагает ввести те значения, которых там нет.

Вот пример набора параметров из файла azuredeploy-parameters.json.

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Укажите имена развертывания Azure и группы ресурсов, расположение Azure и имя папки, в которой находится сохраненный JSON-файл. Затем выполните следующие команды:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

При запуске команды **New-AzureResourceGroupDeployment** значения параметров извлекаются из JSON-файла и начинается выполнение шаблона на их основе. Использование нескольких файлов параметров в разных средах (например, тестовой, рабочей и т. д.) способствует повторному использованию шаблона и упрощает развертывание сложных решений для нескольких сред.

При развертывании следует помнить о том, что необходимо создать новую учетную запись хранения Azure. Поэтому имя, которое вы указываете в качестве параметра учетной записи хранения, должно быть уникальным и отвечать всем требованиям для такой учетной записи (содержать только буквы в нижнем регистре и цифры).

Во время и после развертывания можно проверить все запросы, сделанные на этапе подготовки, включая все возникшие ошибки.

Для этого перейдите на [портал Azure](https://portal.azure.com) и сделайте следующее.

- Щелкните **Обзор** на панели навигации слева, прокрутите вниз и щелкните **Группы ресурсов**.  
- Щелкните только что созданную группу ресурсов, после чего откроется колонка «Группа ресурсов».  
- Щелкнув гистограмму «События» в области **Мониторинг** колонки «Группа ресурсов», вы увидите события, связанные с развертыванием.
- Щелкнув отдельные события, вы можете просмотреть подробную информацию о каждой операции, выполненной с шаблоном.

Если после проверки вам потребуется удалить эту группу и все ее ресурсы (учетную запись хранения, виртуальную машину и виртуальную сеть), выполните следующую команду:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Шаг 3-б. Развертывание кластера DataStax на основе шаблона с помощью интерфейса командной строки Azure

Чтобы выполнить развертывание кластера DataStax с помощью интерфейса командной строки Azure, сначала создайте группу ресурсов, указав для нее имя и расположение.

	azure group create dsc "West US"

Передайте это имя группы ресурсов, расположение шаблона JSON-файла и расположение файла параметров в следующую команду (дополнительную информацию см. в разделе для Azure PowerShell выше):

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Проверить состояние развертывания отдельных ресурсов можно с помощью следующей команды:

	azure group deployment list dsc

## Обзор структуры шаблона и организации файла DataStax

Чтобы создать надежный шаблон диспетчера ресурсов, который можно будет использовать многократно, следует продумать, как будут выполняться несколько сложных и взаимосвязанных задач, необходимых при развертывании комплексного решения, например DataStax. Используя такие функциональные возможности диспетчера ресурсов, как связывание шаблонов, циклическое создание ресурсов и выполнение сценариев с помощью связанных расширений, можно реализовать модульный подход и использовать его практически для любого сложного развертывания на основе шаблона.

В этом разделе описана структура файла azuredeploy.json для кластера DataStax.

### Раздел parameters

В разделе parameters файла azuredeploy.json задаются параметры, используемые в шаблоне. Значения передаются в раздел parameters файла azuredeploy.json во время выполнения шаблона с помощью вышеупомянутого файла azuredeploy-parameters.json.

### Раздел variables

В разделе variables указываются переменные, которые могут использоваться в данном шаблоне. Он содержит несколько полей (типы данных JSON или фрагменты), которым во время выполнения будут присвоены константы или вычисляемые значения. Вот как выглядит раздел variables этого шаблона DataStax:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

При более подробном рассмотрении этого примера можно увидеть два различных подхода. В первом фрагменте переменная **osSettings** является вложенным элементом JSON, содержащим четыре пары «ключ — значение».

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
Во втором фрагменте переменная **scripts** — это массив JSON, в котором каждый элемент будет вычисляться во время выполнения с помощью функции языка шаблонов (concat) и значения другой переменной, а также строковых констант.

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### Раздел resources

Именно в разделе resources выполняется большая часть операций. Если тщательно изучить этот раздел, можно сразу определить два разных элемента. Первый из них — это элемент типа `Microsoft.Resources/deployments`, который по сути вызывает вложенное развертывание внутри основного. В элементе **templateLink** (и связанном с ним свойстве версии) можно указать файл связанного шаблона, который будет вызван с помощью передачи набора параметров в качестве входных данных, как можно увидеть в этом фрагменте.

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

Первый пример позволяет понять, как файл azuredeploy.json в этом сценарии используется для организации взаимодействия, вызывая несколько других шаблонов. Каждый файл отвечает за часть необходимых этапов развертывания.

В частности, для этого развертывания будут использоваться следующие связанные шаблоны:

-	**shared-resource.json** — содержит определения всех ресурсов, которые будут совместно использоваться в развертывании. К ним относятся, например, учетные записи хранения, используемые для хранения дисков ОС и виртуальных сетей для виртуальной машины.
-	**opscenter-resources.json** — развертывает виртуальную машину OpsCenter и все связанные с ней ресурсы, в том числе сетевой интерфейс и общедоступный IP-адрес.
-	**opscenter-install-resources.json** — развертывает расширение виртуальной машины OpsCenter (настраиваемый скрипт для Linux), которое вызовет определенный файл скрипта Bash (opscenter.sh), необходимый для установки службы OpsCenter в этой виртуальной машине.
-	**ephemeral-nodes-resources.json** — развертывает все виртуальные машины в узлах кластера и подключенные ресурсы (сетевые карты, частные IP-адреса и т. д.). Этот шаблон также развернет расширения виртуальной машины (пользовательские сценарии для Linux) и вызовет скрипт Bash (dsenode.sh) для физической установки битов Apache Cassandra в каждом узле.

Давайте рассмотрим использование этого последнего шаблона более детально, так как он наиболее интересен с точки зрения разработки шаблонов. Следует обратить особое внимание на то, как один файл шаблона может развернуть несколько копий ресурсов одного типа и назначить каждому экземпляру уникальные значения для обязательных параметров. Этот принцип называется **циклическим созданием ресурсов**.

Когда шаблон ephemeral-nodes-resources.json вызывается из основного файла azuredeploy.json, в составе списка параметров предоставляется параметр *nodeCount*. В дочернем шаблоне параметр *nodeCount* (количество узлов для развертывания в кластере) будет использоваться в элементе **copy** каждого ресурса, для которого следует создать несколько копий развертывания, как указано в приведенном ниже фрагменте. Для всех параметров, которым необходимы уникальные значения для разных экземпляров развернутых ресурсов, можно использовать функцию **copyindex()**, чтобы получить числовое значение, указывающее текущий индекс в конкретном циклическом создании ресурсов. В фрагменте ниже показано применение этого принципа в нескольких виртуальных машинах, создаваемых для узлов кластера DataStax.

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

Другим важным принципом в создании ресурса является возможность указать зависимости и приоритеты для ресурсов, как можно увидеть в массиве JSON **dependsOn**. В этом шаблоне к каждому узлу будет подключен диск объемом 1 ТБ (см. "dataDisks"), который можно использовать для размещения резервных копий и моментальных снимков экземпляра Apache Cassandra.

Подключенные диски форматируются в ходе операций по подготовке узла, которые запускаются при выполнении файла скрипта dsenode.sh. Первая строка этого сценария вызывает другой сценарий:

	bash vm-disk-utils-0.1.sh

Файл vm-disk-utils-0.1.sh содержится в папке **shared\_scripts\\ubuntu** в репозитории GitHub azure-quickstart-templates и включает в себя очень полезные функции для подключения, форматирования и чередования дисков. Эти функции можно использовать во всех шаблонах в репозитории.

Еще один интересный фрагмент, который стоит рассмотреть, связан с расширениями виртуальной машины CustomScriptForLinux. Они устанавливаются как отдельный тип ресурса с зависимостью от каждого узла кластера (и экземпляра OpsCenter). Они используют тот же механизм циклического создания ресурсов, что и виртуальные машины:

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

Ознакомившись с другими файлами в этом развертывании, можно понять, какие принципы и методы используются, чтобы организовывать сложные стратегии развертывания решений с несколькими узлами на базе любой технологии и чтобы управлять ими с помощью шаблонов диспетчера ресурсов Azure. Хотя это не обязательно, мы советуем структурировать файлы шаблона с помощью подхода, указанного на следующей схеме:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

По сути, в рамках этого подхода необходимо сделать следующее:

-	определить основной файл шаблона в качестве центральной точки оркестрации для всех определенных операций развертывания за счет использования создания связей между шаблонами для вызова операций выполнения подшаблона;
-	создать определенные файлы шаблона, которые развернут все ресурсы, используемые в остальных задачах конкретного развертывания (учетные записи хранения, конфигурация виртуальной сети и т. д.). Эти файлы можно повторно использовать в развертываниях со схожими требованиями в отношении общей инфраструктуры.
-	включить необязательные шаблоны ресурсов для отдельных требований конкретного заданного ресурса;
-	создать специальные шаблоны для одинаковых элементов группы ресурсов (узлы в кластере и т. д.), которые используют циклическое создание ресурсов для развертывания нескольких экземпляров с уникальными свойствами;
-	использовать расширения для развертывания сценариев и создать сценарии для каждой из технологий для всех задач, выполняемых после развертывания (установка продукта, конфигурация и т. д.).

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

<!---HONumber=Oct15_HO2-->