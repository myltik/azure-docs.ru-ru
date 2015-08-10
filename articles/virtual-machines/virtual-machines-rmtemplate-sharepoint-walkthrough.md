<properties
	pageTitle="Шаблон диспетчера ресурсов для трехсерверной фермы Sharepoint"
	description="Последовательно изучите структуру шаблона диспетчера ресурсов Azure для трехсерверной фермы SharePoint."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Шаблон диспетчера ресурсов для трехсерверной фермы Sharepoint

В этом разделе последовательно рассматривается структура файла шаблона azuredeploy.json для трехсерверной фермы SharePoint. Кроме того, просмотреть содержимое этого шаблона можно в браузере [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json).

Для того чтобы ознакомиться с локальной копией файла azuredeploy.json, в качестве альтернативы укажите локальную папку для расположения файла и создайте его (например, C:\\Azure\\Templates\\SharePointFarm). Введите имя папки и запустите эти команды в командной строке Azure PowerShell.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

Откройте шаблон azuredeploy.json в текстовом редакторе или с помощью другого выбранного средства. Ниже описана структура файла шаблона и назначение каждого раздела.

## Раздел parameters

В разделе parameters указываются параметры, используемые для ввода данных в этот шаблон. Можно определить до 50 параметров. Ниже приведен пример параметра для расположения Azure:

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## Раздел variables

В разделе variables указываются переменные, которые могут использоваться в данном шаблоне. Можно определить до 100 переменных. Ниже приведены некоторые примеры:

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## Раздел resources

В разделе **resources** определяется информация, необходимая для развертывания ресурсов для фермы SharePoint в группе ресурсов. Можно определить не более 250 ресурсов, и для определения зависимостей ресурсов можно использовать до 5 уровней вложенности.

Этот раздел содержит следующие подразделы:

### Microsoft.Storage/storageAccounts  

Этот раздел создает новую учетную запись хранения для всех ресурсов виртуального жесткого диска и диска для фермы. Ниже приведен код JSON для учетной записи хранения:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Эти разделы создают набор общедоступных IP-адресов, по которым к каждой виртуальной машине можно обратиться через Интернет. Пример:

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

Эти разделы создают три группы доступности, по одной для каждого уровня развертывания:

- Контроллеры домена Active Directory
- Кластер SQL Server
- Серверы SharePoint

Пример:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Этот раздел создает облачную виртуальную сеть с тремя подсетями (по одной для каждого уровня развертывания), в которой размещаются виртуальные машины. Ниже приведен код JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

Эти разделы создают экземпляры подсистемы балансировки нагрузки для каждой виртуальной машины, чтобы обеспечить фильтрацию NAT и трафика для входящего трафика из Интернета. Для каждой подсистемы балансировки нагрузки параметры задают внешний интерфейс, серверную часть и правила для входящих подключений NAT. Например, есть правила трафика удаленных рабочих столов для каждой виртуальной машины и правило, разрешающее входящий веб-трафик (TCP-порт 80) из Интернета. Ниже приведен пример сервера SharePoint:


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

Эти разделы создают один сетевой интерфейс для каждой виртуальной машины и настраивают статический IP-адрес контроллера домена. Ниже приведен пример сетевого интерфейса для контроллера домена:

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

Эти разделы создают и настраивают три виртуальные машины в развертывании.

Первый раздел создает и настраивает контроллер домена, который:

- указывает учетную запись хранения, группу доступности, сетевой интерфейс и экземпляр подсистемы балансировки нагрузки;
- добавляет дополнительный диск;
- выполняет сценарий PowerShell для настройки контроллера домена.

Ниже приведен код JSON:

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

Дополнительный раздел после каждого контроллера домена, начинающийся с **"name": "UpdateVNetDNS"**, настраивает DNS-сервер виртуальной сети для использования статического IP-адреса контроллера домена.

Следующий раздел **"type": "Microsoft.Compute/virtualMachines"** создает виртуальные машины SQL Server в развертывании, а также:

- указывает учетную запись хранения, группу доступности, подсистему балансировки нагрузки, виртуальную сеть и сетевой интерфейс;
- добавляет дополнительный диск.

Дополнительные разделы **"Microsoft.Compute/virtualMachines/extensions"** вызывают сценарий PowerShell для настройки сервера SharePoint.

Следующий раздел **"type": "Microsoft.Compute/virtualMachines"** создает виртуальную машину SharePoint в развертывании, указывая учетную запись хранения, группу доступности, подсистему балансировки нагрузки, виртуальную сеть и сетевой интерфейс. Дополнительный раздел **"Microsoft.Compute/virtualMachines/extensions"** вызывает сценарий PowerShell для настройки фермы PowerShell.

Обратите внимание на общую структуру подразделов в разделе **"resources"** JSON-файла:

1.	Создание элементов инфраструктуры Azure, которые необходимы для поддержки нескольких виртуальных машин (учетная запись хранения, общедоступные IP-адреса, группы доступности, виртуальная сеть, сетевые интерфейсы, экземпляры подсистемы балансировки нагрузки).
2.	Создание виртуальной машины контроллера домена, которая использует ранее созданные общие и специализированные элементы инфраструктуры Azure, добавляет диски данных и запускает сценарий PowerShell. А также обновление виртуальной сети для использования статического IP-адреса контроллера домена.
3.	Создание виртуальной машины SQL Server, которая использует ранее созданные общие и специализированные элементы инфраструктуры Azure, созданные для контроллера домена, добавляет диски данных и запускает сценарий PowerShell для настройки SQL Server.
4.	Создание виртуальной машины сервера SharePoint, которая использует ранее созданные общие и специализированные элементы инфраструктуры Azure и запускает сценарий PowerShell для настройки фермы SharePoint.

Ваш шаблон JSON для построения многоуровневой инфраструктуры в Azure должен выполнить те же действия:

1.	Создайте общие (учетная запись хранения, виртуальная сеть), относящиеся к уровню (группы доступности) и относящиеся к виртуальным машинам (общедоступные IP-адреса, группы доступности, сетевые интерфейсы и экземпляры подсистемы балансировки нагрузки) элементы инфраструктуры Azure, которые необходимы для вашего развертывания.
2.	Для каждого уровня в приложения (например, аутентификация, базы данных, Интернет) создайте и настройте серверы с помощью общих (учетная запись хранения, виртуальная сеть), относящихся к уровню (группа доступности) и относящихся к виртуальным машинам (общедоступные IP-адреса, сетевые интерфейсы, экземпляры подсистемы балансировки нагрузки) элементов.

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md)

[Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=July15_HO5-->