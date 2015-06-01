<properties
	pageTitle="Использование решения DataStax в Ubuntu с шаблоном диспетчера ресурсов"
	description="Информация о том, как легко развернуть кластер DataStax в виртуальных машинах Ubuntu с помощью Azure PowerShell или интерфейса командной строки Azure и диспетчера ресурсов шаблона"
	services="multiple"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# Использование решения DataStax в Ubuntu с шаблоном диспетчера ресурсов

Компания DataStax — известный, ведущий в отрасли разработчик и поставщик решений на базе коммерчески поддерживаемой, корпоративной распределенной базы данных NoSQL Apache Cassandra™ с открытым исходным кодом, которая доказала свою гибкость, непрерывность работы и предсказуемую масштабируемость до любого размера. Существует две версии решений DataStax: Enterprise (DSE) и Community (DSC). Они предоставляют такие возможности, как вычисление в оперативной памяти, безопасность корпоративного уровня, быстрые и мощные интегрированные инструменты аналитики и поиск в корпоративной среде.

Кроме возможностей, доступных в Azure Marketplace, теперь вы также можете с легкостью развернуть кластер DataStax в виртуальных машинах Ubuntu с помощью Azure PowerShell или интерфейса командной строки Azure и шаблона диспетчера ресурсов.

Топология недавно развернутых на основе этого шаблона кластеров описана на следующей схеме. Вы можете легко создать другие топологии, изменив представленный подход с учетом индивидуальных требований.

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

По сути, с помощью параметра можно определить количество узлов, которые будут развернуты в новом кластере Apache Cassandra. Кроме того, экземпляр службы DataStax Operation Center будет также развернут в изолированной виртуальной машине в одной и той же виртуальной сети, что дает вам возможность следить за состоянием кластера и всех отдельных узлов, добавлять или удалять узлы и выполнять все административные задачи, связанные с этим кластером.

После завершения развертывания вы можете получить доступ к экземпляру виртуальной машины Datastax Operations Center с помощью заданного DNS-адреса. В виртуальной машине OpsCenter включен SSH-порт 22 и HTTPS-порт 8443. DNS-адрес для Operations Center будет включать в себя DNS-имя и регион, которые будут вводится в качестве параметров в формате `{dnsName}.{region}.cloudapp.azure.com` при создании развертывания на основе этого шаблона. Если вы создали развертывание и указали для параметра `dnsName` значение datastax в регионе West US, вы можете получить доступ к виртуальной машине Datastax Operations Center для развертывания по адресу `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]Сертификат, используемый в развертывании, является самозаверяющим сертификатом, который создаст предупреждение в браузере. Чтобы заменить этот сертификат собственным SSL-сертификатом, выполните процедуры, указанные на веб-сайте [Datastax](http://www.datastax.com/).

Прежде чем углубляться в дополнительную информацию, связанную с диспетчером ресурсов Azure и шаблоном, который мы использовали для этого развертывания, убедитесь, что Azure, PowerShell и интерфейс командной строки Azure настроены и готовы к работе.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Создание кластера Cassandra на основе DataStax с помощью шаблона диспетчера ресурсов и Azure PowerShell

Выполните следующие шаги, чтобы создать кластер Apache Cassandra на основе DataStax с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью Azure PowerShell.

### Шаг 1. Скачивание JSON-файла для шаблона и других файлов

Назначьте локальную папку в качестве расположения для JSON-файлов шаблонов и других файлов и создайте ее (например, с путем C:\\Azure\\Templates[объект_создания]).

Введите имя папки, а затем скопируйте его и выполните следующие команды:

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

В качестве альтернативы вы можете также клонировать репозиторий шаблона с помощью выбранного клиента GIT, например:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

После завершения найдите папку datastax-on-ubuntu по следующему пути: C:\\Azure\\Templates.

### Шаг 2. Ознакомление с параметрами шаблона (необязательно)

При развертывании нестандартных решений, таких как кластер Apache Cassandra на основе DataStax, вы должны указать набор параметров конфигурации для работы с множеством необходимых параметров. Объявив эти параметры в определении шаблона, при выполнении развертывания вы можете указать значения во внешнем файле или в командной строке.

Если вы просмотрите раздел parameters в верхней части файла azuredeploy.json, вы обнаружите набор параметров, необходимых шаблону для настройки кластера DataStax. Ниже приведен пример данного раздела в шаблоне azuredeploy.json:

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
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
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

По описанию обязательных параметров, включая такую информацию, как типы данных, допустимые значения и т. д., становится понятно, что этот раздел будет очень полезен для выполнения любой задачи проверки, связанной со значениями параметров, передаваемыми при выполнении шаблона в интерактивном режиме (например, в PowerShell или интерфейсе командной строки Azure), а также для любого пользовательского интерфейса с возможностями самообнаружения, который можно создать динамически путем синтаксического анализа списка обязательных параметров и их описаний.

### Шаг 3. Развертывание нового кластера DataStax с использованием шаблона

Подготовка файла параметров для развертывания означает создание JSON-файла, содержащего значения среды выполнения для всех параметров, которые затем как единая сущность передаются команде развертывания.

Ниже приведен пример списка значений, который можно найти в файле azuredeploy-parameters.json:

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

Введите имена развертывания Azure и группы ресурсов, а также расположение Azure и имя папки, в которой находится сохраненный JSON-файл, и выполните следующие команды:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

При выполнении команды **New-AzureResourceGroupDeployment** значения параметров извлекаются из JSON-файла, и начинается выполнение шаблона соответствующим образом. Определение и использование нескольких файлов параметров в разных средах (например, тестовая среда, рабочая среда и т. д.) способствует повторному использованию шаблона и упрощает сложные решения для нескольких сред.

При развертывании следует помнить, что требуется создать новую учетную запись хранения Azure. Поэтому предоставляемое имя, указываемое в качестве параметра учетной записи хранения, должно быть уникальным и отвечать всем требованиям для учетной записи хранения Azure.

Во время и после развертывания можно проверить все запросы, сделанные при подготовке, включая все случившиеся ошибки.

Для этого перейдите на [портал Azure](https://portal.azure.com) и выполните следующее:

- Щелкните «Обзор» на панели навигации слева, прокрутите вниз и щелкните «Группы ресурсов».  
- После того, как вы щелкните только что созданную группу ресурсов, откроется колонка «Группа ресурсов».  
- Щелкнув гистограмму «События» в колонке «Группа ресурсов» в области «Мониторинг», можно увидеть события для вашего развертывания.
- Щелкнув отдельные события, можно просмотреть подробную информацию о каждой отдельной операции, выполненной с шаблоном.

Если после выполнения тестов необходимо удалить эту группу ресурсов и все ее ресурсы (учетную запись хранения, виртуальную машину и виртуальную сеть), выполните следующую команду:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Шаг 3 б. Развертывание кластера DataStax с использованием шаблона диспетчера ресурсов в интерфейсе командной строки Azure

Подход, при котором необходимо развернуть кластер Apache Cassandra с помощью интерфейса командной строки Azure, функционально ничем не отличается от подхода PowerShell, указанного выше. Сначала требуется создать группу ресурсов, указав ее имя и расположение:

	azure group create dsc "West US"

Затем необходимо вызвать создание развертывания и передать имя группы ресурсов, файл параметров и шаблон, как показано ниже:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Можно также проверить состояние отдельных развертываний, вызвав следующую команду:

	azure group deployment list dsc

## Обзор структуры шаблона и организации файлов, созданных для развертывания DataStax в Ubuntu

Чтобы сформировать надежный подход к проектированию шаблонов диспетчера ресурсов, который можно использовать несколько раз, следует подумать над организацией ряда сложных и взаимосвязанных задач, необходимых при развертывании сложного решения, такого как DataStax. Используя такие возможности ARM, как **создание связей между шаблонами**, **циклическое создание ресурсов** и выполнение сценария с помощью связанных расширений, можно реализовать модульный подход, который можно использовать повторно для практически любого сложного развертывания на основе шаблона.

На этой схеме представлены связи между всеми файлами, скачанными с GitHub для этого развертывания:

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

Мы уже упоминали о роли файла **azuredeploy-parameters.json**, который будет использоваться для передачи заданного набора значений параметров во время выполнения шаблона. Но основная часть этого подхода, используемого для развертывания, содержится в файле **azuredeploy.json**. Мы пропустим раздел параметров, так как он уже описывался в этом документе ранее. Далее представлен раздел **variables**. По сути, он содержит несколько полей (типы данных JSON или фрагменты), которым во время выполнения будут присвоены константы или вычисляемые значения, как видно в следующем примере:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/trentmswanson/azure-quickstart-templates/master/datastax-on-ubuntu/",
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

При более подробном рассмотрении этого примера можно увидеть два различных подхода. В первом фрагменте переменной osSettings будет присвоен вложенный элемент JSON, содержащий 4 пары «ключ-значение»:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
Во втором фрагменте переменная scripts — это массив JSON, в котором отдельные элементы будут вычисляться во время выполнения с помощью функции языка шаблонов (concat) и значения другой переменной, а также строковых констант:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

Именно в разделе **resources** выполняется большая часть операций. Если внимательно просмотреть этот раздел, можно незамедлительно обнаружить два различных элемента. Первый из них — это элемент типа `Microsoft.Resources/deployments`, который по сути означает вызов вложенного развертывания в основном развертывании. В элементе `templateLink` (и связанном с ним свойстве версии) можно указать файл связанного шаблона, который будет вызван для передачи набора параметров в качестве входных данных, как можно увидеть в этом фрагменте:

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

В первом примере понятно, как файл **azuredeploy.json** в этом сценарии выполнял роль механизма оркестрации, вызывая ряд других файлов шаблонов, каждый из которых отвечает за часть операций, необходимых для развертывания.

В частности, для этого развертывания будут использоваться следующие связанные шаблоны:

-	**shared-resource.json** — содержит определения всех ресурсов, которые будут совместно использоваться в развертывании. Например, учетные записи хранения, используемые для хранения дисков операционной системы и виртуальных сетей виртуальной машины.
-	**opscenter-resources.json** — развертывает виртуальную машину OpsCenter и все связанные с ней ресурсы, такие как сетевой интерфейс, общедоступный IP-адрес и т. д.
-	**opscenter-install-resources.json** — развертывает расширение виртуальной машины OpsCenter (пользовательский сценарий для Linux), которое вызовет определенный файл сценария Bash (**opscenter.sh**), необходимый для установки службы OpsCenter в этой виртуальной машине.
-	**ephemeral-nodes-resources.json** — развертывает все виртуальные машины в узлах кластера и подключенные ресурсы (например, сетевые карты, частные IP-адреса и т. д.). Этот шаблон также развернет расширения виртуальной машины (пользовательские сценарии для Linux) и вызовет сценарий Bash (**dsenode.sh**) для физической установки битов Apache Cassandra в каждом узле.

Давайте рассмотрим этот последний шаблон более детально, так как он наиболее интересен с точки зрения разработки шаблонов. Следует обратить особое внимание на то, как один файл шаблона может развернуть несколько копий ресурсов одного типа и назначить каждому экземпляру уникальные значения для обязательных параметров. Этот принцип называется **циклическим созданием ресурсов**.

При вызове шаблона **ephemeral-nodes-resources.json** из основного файла **azuredeploy.json** по сути предоставляется параметр **nodeCount** в качестве части списка параметров. В дочернем шаблоне этот параметр (количество узлов для развертывания в кластере) будет использоваться в элементе **copy** каждого ресурса, для которого следует создать несколько копий развертывания, как указано в приведенном ниже фрагменте. Для всех параметров, для которых необходимо указать уникальные значения в различных экземплярах развернутых ресурсов, затем можно использовать функцию **copyindex()**, чтобы получить числовое значение, указывающее текущий индекс в конкретном циклическом создании ресурсов. В следующем фрагменте этот принцип применяется при нескольких созданиях виртуальных машин для узлов кластера:

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

Другим важным принципом в создании ресурса является возможность указать зависимости и приоритеты для ресурсов, как можно увидеть в массиве JSON **dependsOn**. В этом шаблоне к каждому узлу будет подключен диск объемом 1 ТБ (см. `dataDisks`), который можно использовать для размещения резервных копий и моментальных снимков из экземпляра Apache Cassandra.

Подключенные диски форматируются в ходе операций по подготовке узла, которые запускаются при выполнении файла сценария **dsenode.sh**. Первая строка этого сценария, к тому же, вызывает другой сценарий:

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh содержится в папке **shared_scripts\\ubuntu** в репозитории GitHub azure-quickstart-tempates и включает в себя очень полезные функции для подключения, форматирования и чередования дисков, которые можно использовать повторно при каждом выполнении подобных задач в ходе создания шаблона.

Еще один интересный фрагмент, который стоит рассмотреть — фрагмент, связанный с расширениями виртуальной машины CustomScriptForLinux. Они устанавливаются как отдельный тип ресурсов с зависимостью на каждом узле кластера (и в экземпляре OpsCenter). Здесь используется тот же механизм циклического создания ресурсов, который описан для виртуальных машин:

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

Все прочие файлы шаблона, необходимые для этого развертывания, создают отдельные экземпляры всех необходимых ресурсов, поэтому их можно считать упрощенной версией файла **ephemeral-nodes-resources.json**.

Ознакомившись с другими файлами, включенными в это развертывание, вы сможете понять все детали и все рекомендации, необходимые для организации сложных стратегий развертывания решений для нескольких узлов, которые созданы на основе любой технологии, и управления ими, с использованием шаблонов диспетчера ресурсов Azure. Хотя это не обязательно, мы советуем структурировать файлы шаблона с помощью подхода, указанного на следующей схеме:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

По сути, в рамках этого подхода вы должны сделать следующее:

-	Определить основной файл шаблона в качестве центральной точки оркестрации для всех определенных операций развертывания за счет использования создания связей между шаблонами для вызова операций выполнения подшаблона.
-	Создать определенные файлы шаблона, которые развернут все ресурсы, используемые в остальных задачах конкретного развертывания (например, учетные записи хранения, конфигурация виртуальной сети и т. д.). Эти файлы можно повторно использовать в развертываниях со схожими требованиями в отношении общей инфраструктуры.
-	Включить необязательные шаблоны ресурсов для отдельных требований конкретного заданного ресурса.
-	Создать специальные шаблоны для одинаковых элементов группы ресурсов (узлы в кластере и т. д.), которые используют циклическое создание ресурсов для развертывания нескольких экземпляров с уникальными свойствами.
-	Использовать расширения для развертывания сценариев и создать сценарии, относящиеся к каждой из технологий, для всех задач, выполняемых после развертывания (например, установка продукта, конфигурация и т. д.).

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=52-->
