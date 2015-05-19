<properties 
	pageTitle="Шаблон диспетчера ресурсов для высокодоступной фермы SharePoint" 
	description="Последовательно изучите структуру файла шаблона диспетчера ресурсов Azure для высокодоступной фермы SharePoint." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Шаблон диспетчера ресурсов для высокодоступной фермы SharePoint

В этом разделе последовательно рассматривается структура файла шаблона azuredeploy.json для высокодоступной фермы SharePoint.

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

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## Раздел resources

В разделе **resources** определяется информация, необходимая для развертывания ресурсов для фермы SharePoint в группе ресурсов. Можно определить не более 250 ресурсов, и для определения зависимостей ресурсов можно использовать до 5 уровней вложенности.

Этот раздел содержит следующие подразделы:

### Microsoft.Storage/storageAccounts  

Этот раздел создает новую учетную запись хранения для всех ресурсов виртуального жесткого диска и диска для фермы. Ниже приведен код JSON для учетной записи хранения:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Эти разделы создают набор общедоступных IP-адресов, по которым к каждой виртуальной машине можно обратиться через Интернет. Пример:

	{
		"apiVersion": "2014-12-01-preview",
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

Эти разделы создают четыре группы доступности, по одной для каждого уровня развертывания:

- Контроллеры домена Active Directory
- Кластер SQL Server
- Серверы уровня приложений
- Серверы уровня интернета

Пример:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Этот раздел создает облачную виртуальную сеть с четырьмя подсетями \(по одной для каждого уровня развертывания\), в которой размещаются виртуальные машины. Ниже приведен код JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

Эти разделы создают экземпляры подсистемы балансировки нагрузки для каждой виртуальной машины, чтобы обеспечить фильтрацию NAT и трафика для входящего трафика из Интернета. Для каждой подсистемы балансировки нагрузки параметры задают внешний интерфейс, серверную часть и правила для входящих подключений NAT. Например, существуют правила трафика удаленных рабочих столов для каждой виртуальной машины и правило, разрешающее входящий веб-трафик \(TCP-порт 80\) из Интернета для серверов уровня Интернета. Ниже приведен пример сервера уровня Интернета:

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

Эти разделы создают один сетевой интерфейс для каждой виртуальной машины и настраивают статические IP-адреса контроллеров домена. Ниже приведен пример сетевого интерфейса для основного контроллера домена:

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

Эти разделы создают и настраивают девять виртуальных машин в развертывании.

Первые два основных раздела создают и настраивают контроллеры домена в развертывании. Каждый раздел:

- Указывает учетную запись хранения, группу доступности, сетевой интерфейс и экземпляр подсистемы балансировки нагрузки для каждой виртуальной машины контроллера домена.
- Добавляет дополнительный диск в каждую виртуальную машину контроллера домена.
- Запускает сценарий PowerShell для настройки виртуальных машин в качестве контроллеров домена.

Вот пример сетевого интерфейса для основного контроллера домена:

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


Дополнительный раздел после каждого контроллера домена, начинающийся с **"name": "UpdateVNetDNS"**, настраивает DNS-серверы виртуальной сети для использования статических IP-адресов двух контроллеров домена.

Следующие три раздела **"type": "Microsoft.Compute/virtualMachines"** создают виртуальные машины кластера SQL Server в развертывании. Каждый раздел:

- Указывает учетную запись хранения, группу доступности, подсистему балансировки нагрузки, виртуальную сеть и сетевой интерфейс для каждой виртуальной машины.
- Добавляет два дополнительных диска для каждого экземпляра SQL Server.

Дополнительные разделы **"Microsoft.Compute/virtualMachines/extensions"** вызывают сценарий PowerShell для настройки виртуальных машин кластера SQL Server, кластера SQL Server и включения групп доступности AlwaysOn.

Следующие четыре раздела **"type": "Microsoft.Compute/virtualMachines"** создают виртуальные машины фермы SharePoint в развертывании. Каждый раздел указывает учетную запись хранения, группу доступности, подсистему балансировки нагрузки и сетевой интерфейс для каждой виртуальной машины.

Дополнительные разделы **"Microsoft.Compute/virtualMachines/extensions"** вызывают сценарий PowerShell для настройки серверов SharePoint в качестве фермы SharePoint.

Обратите внимание на общую структуру подразделов в разделе **"resources"** JSON-файла:

1.	Создание элементов инфраструктуры Azure, которые необходимы для поддержки нескольких виртуальных машин \(учетная запись хранения, общедоступные IP-адреса, группы доступности, виртуальная сеть, сетевые интерфейсы, экземпляры подсистемы балансировки нагрузки\).
2.	Создание виртуальных машин контроллера домена, которые используют ранее созданные общие и специализированные элементы инфраструктуры Azure, добавление дисков данных и запуск сценариев PowerShell. А также обновление виртуальной сети для использования статических IP-адресов контроллеров домена.
3.	Создание виртуальных машин кластера SQL Server, которые используют ранее созданные общие и конкретные элементы Azure, созданные для контроллеров домена, добавление дисков данных и запуск сценариев PowerShell для настройки кластера и групп доступности AlwaysOn SQL Server.
4.	Создание виртуальных машин сервера SharePoint, которые используют ранее созданные общие и специализированные элементы инфраструктуры Azure, добавление дисков данных и запуск сценариев PowerShell для настройки фермы SharePoint.

Ваш шаблон JSON для построения многоуровневой инфраструктуры в Azure должен выполнить те же действия:

1.	Создайте общие \(учетная запись хранения, виртуальная сеть\), относящиеся к уровню \(группы доступности\) и относящиеся к виртуальным машинам \(общедоступные IP-адреса, группы доступности, сетевые интерфейсы и экземпляры подсистемы балансировки нагрузки\) элементы инфраструктуры Azure, которые необходимы для вашего развертывания.
2.	Для каждого уровня в приложения \(например, аутентификация, базы данных, Интернет\) создайте и настройте серверы с помощью общих \(учетная запись хранения, виртуальная сеть\), относящихся к уровню \(группа доступности\) и относящихся к виртуальным машинам \(общедоступные IP-адреса, сетевые интерфейсы, экземпляры подсистемы балансировки нагрузки\) элементов.

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md)

[Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
