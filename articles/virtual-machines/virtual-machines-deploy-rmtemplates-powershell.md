<properties 
	pageTitle="Развертывание виртуальных машин Azure и управление ими с использованием шаблонов диспетчера ресурсов и PowerShell" 
	description="С легкостью развертывайте наиболее распространенные наборы конфигураций для виртуальных машин Azure и управляйте ими с помощью шаблонов диспетчера ресурсов и PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="josephd"/>

# Развертывание виртуальных машин и управление ими с использованием шаблонов диспетчера ресурсов Azure и PowerShell

В этой статье рассказывается, как с помощью PowerShell и шаблонов диспетчера ресурсов Azure автоматизировать стандартные задачи по развертыванию виртуальных машин Azure и управлению ими. Дополнительные шаблоны см. в статье [Шаблоны быстрого запуска Azure](http://azure.microsoft.com/documentation/templates/) и в разделе [Платформы приложений](virtual-machines-app-frameworks.md).

- [Развертывание виртуальной машины под управлением ОС Windows](#windowsvm)
- [Создание образа настраиваемой виртуальной машины](#customvm)
- [Развертывание приложения для нескольких виртуальных машин, использующего виртуальную сеть и внешнюю подсистему балансировки нагрузки](#multivm)
- [Обновление виртуальной машины, развернутой с использованием шаблона диспетчера ресурсов](#updatevm)
- [Удаление группы ресурсов](#removerg)
- [Вход в виртуальную машину](#logon)
- [Отображение информации о виртуальной машине](#displayvm)
- [Запуск виртуальной машины](#start)
- [Останов виртуальной машины](#stop)
- [Перезапуск виртуальной машины](#restart)
- [Удаление виртуальной машины](#delete)

Перед началом работы убедитесь, что среда Azure PowerShell готова к работе.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Общая информация о шаблонах диспетчера ресурсов Azure и группах ресурсов

Большинство приложений, развертываемых и используемых в Microsoft Azure, созданы на основе сочетания облачных ресурсов различных типов (например, одной или нескольких виртуальных машин и учетных записей хранения, базы данных SQL или виртуальной сети). С помощью шаблонов диспетчера ресурсов Azure можно развертывать эти ресурсы и управлять ими совокупно, используя описание ресурсов в формате JSON и связанные параметры конфигурации и развертывания.

Определив шаблон ресурсов на основе JSON, вы можете выполнить его и развернуть определенные в нем ресурсы в Azure с помощью команды PowerShell. Вы можете выполнять следующие команды отдельно в командной оболочке PowerShell или интегрировать их в сценарий, который содержит дополнительную логику автоматизации.

Ресурсы, созданные с использованием шаблонов диспетчера ресурсов Azure, можно развернуть как в новой, так и в существующей группе ресурсов Azure. *Группа ресурсов Azure* позволяет управлять несколькими развернутыми ресурсами совокупно в качестве логической группы. Благодаря этому вы можете управлять общим жизненным циклом группы или приложения и использовать API управления, которые позволяют:

- остановить, запустить или удалить все ресурсы в группе одновременно; 
- применить правила управления доступом на основе ролей (RBAC) для блокировки разрешений безопасности для ресурсов; 
- вести аудит операций; 
- отмечать ресурсы с помощью дополнительных метаданных для более точного отслеживания. 

Дополнительную информацию о диспетчере ресурсов Azure см. [здесь](virtual-machines-azurerm-versus-azuresm.md). Если вас интересует разработка шаблонов, см. раздел [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

## <a id="windowsvm"></a>ЗАДАЧА: развертывание виртуальной машины под управлением ОС Windows

Используйте указания в этих разделах для развертывания новой виртуальной машины Azure с помощью шаблона диспетчера ресурсов и Azure PowerShell. Этот шаблон позволяет создать одну виртуальную машину в новой виртуальной сети с единой подсетью.

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)
 
Выполните следующие шаги, чтобы создать виртуальную машину Windows с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью Azure PowerShell.

### Шаг 1. Поиск шаблона в JSON-файле

Далее приведено содержимое JSON-файла для шаблона.

	{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
            "type": "string",
            "metadata": {
                "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
               "Description": "Username for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Password for the Virtual Machine."
            }
        },
        "dnsNameForPublicIP": {
            "type": "string",
            "metadata": {
                  "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "windowsOSVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1", 
                "2012-Datacenter", 
                "2012-R2-Datacenter", 
                "Windows-Server-Technical-Preview"
            ],
            "metadata": {
                "Description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
            }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "MicrosoftWindowsServer", 
        "imageOffer": "WindowsServer", 
        "OSDiskName": "osdiskforwindowssimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16", 
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyWindowsVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",        
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },    
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[variables('publicIPAddressName')]",
            "location": "[variables('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[variables('virtualNetworkName')]",
            "location": "[variables('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[variables('nicName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
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
            "name": "[variables('vmName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                    "computername": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('imagePublisher')]",
                        "offer": "[variables('imageOffer')]",
                        "sku" : "[parameters('windowsOSVersion')]",
                        "version":"latest"
                    },
                   "osDisk" : {
                        "name": "osdisk",
                        "vhd": {
                            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
	} 


### Шаг 2. Создание виртуальной машины с помощью шаблона

Введите имена развертывания Azure и группы ресурсов, а также расположение центра обработки данных Azure и выполните следующие команды.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

При выполнении команды **New-AzureResourceGroupDeployment** вы увидите запрос на ввод значений для параметров раздела parameters, который находится в JSON-файле. Если вы указали значения всех необходимых параметров, команда создаст группу ресурсов и виртуальную машину.

Ниже приведен пример.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Должно отобразиться примерно следующее:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	
	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Теперь в новой группе ресурсов есть новая виртуальная машина Windows с именем MyWindowsVM.

## <a id="customvm"></a>ЗАДАЧА: создание образа настраиваемой виртуальной машины

Используйте указания в этих разделах для создания образа настраиваемой виртуальной машины в Azure с использованием шаблона диспетчера ресурсов с помощью Azure PowerShell. Этот шаблон позволяет создать одну виртуальную машину из указанного виртуального жесткого диска.

### Шаг 1. Поиск шаблона в JSON-файле

Далее приведено содержимое JSON-файла для шаблона.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
	    "parameters": {
	        "osDiskVhdUri": {
	            "type": "string",
	            "metadata": {
	                "Description": "Uri of the existing VHD"
	            }
	        },
	        "osType": {
	            "type": "string",
	            "allowedValues": [
	                "windows",
	                "linux"
	            ],
	            "metadata": {
	                "Description": "Type of OS on the existing vhd"
	            }
	        },
	        "location": {
	            "type": "String",
	            "defaultValue": "West US",
	            "metadata": {
	                "Description": "Location to create the VM in"
	            }
	        },
	        "vmSize": {
	            "type": "string",
	            "defaultValue": "Standard_A2",
	            "metadata": {
	                "Description": "Size of the VM"
	            }
	        },
	        "vmName": {
	            "type": "string",
	            "defaultValue": "myVM",
	            "metadata": {
	                "Description": "Name of the VM"
	            }
	        },
	        "nicName": {
	            "type": "string",
	            "defaultValue": "myNIC",
	            "metadata": {
	                "Description": "NIC to attach the new VM to"
	            }
	        }
	    },
	    "resources": [{
	        "apiVersion": "2014-12-01-preview",
	        "type": "Microsoft.Compute/virtualMachines",
	        "name": "[parameters('vmName')]",
	        "location": "[parameters('location')]",
	        "properties": {
	            "hardwareProfile": {
	                "vmSize": "[parameters('vmSize')]"
	            },
	            "storageProfile": {
	                "osDisk": {
	                    "name": "[concat(parameters('vmName'),'-osDisk')]",
	                    "osType": "[parameters('osType')]",
	                    "caching": "ReadWrite",
	                    "vhd": {
	                        "uri": "[parameters('osDiskVhdUri')]"
	                    }
	                }
	            },
	            "networkProfile": {
	                "networkInterfaces": [{
	                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
	                }]
	            }
	        }
	    }]
	}

### Шаг 2. Получение виртуального жесткого диска

Для виртуальных машин под управлением ОС Windows см. раздел [Создание и передача виртуального жесткого диска Windows Server в Azure](virtual-machines-create-upload-vhd-windows-server.md).

Для виртуальных машин под управлением ОС Linux см. раздел [Создание и передача виртуального жесткого диска Linux в Azure](virtual-machines-linux-create-upload-vhd.md).

### Шаг 3. Создание виртуальной машины с помощью шаблона

Чтобы создать виртуальную машину на основе виртуального жесткого диска, замените элементы, выделенные символами < и >, своими данными и выполните указанные ниже команды.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Появится запрос на указание значений параметров в разделе parameters JSON-файла. Если вы указали значения всех параметров, диспетчер ресурсов Azure создаст группу ресурсов и виртуальную машину.

Пример:

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Появятся такие сведения:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

## <a id="multivm"></a>ЗАДАЧА: развертывание приложения для нескольких виртуальных машин, использующего виртуальную сеть и внешний балансировщик нагрузки

Используйте указания в этих разделах для развертывания приложения для нескольких виртуальных машин, при котором используется виртуальная сеть и балансировщик нагрузки, с помощью Azure PowerShell и шаблона диспетчера ресурсов. Этот шаблон позволяет создать две виртуальные машины в новой виртуальной сети с единой подсетью в новой облачной службе и добавить их во внешний набор балансировки нагрузки для входящего трафика TCP-порта 80.

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Выполните следующие шаги, чтобы развернуть приложение для нескольких виртуальных машин, которое использует виртуальную сеть и подсистему балансировки нагрузки, с помощью шаблона диспетчера ресурсов в репозитории шаблонов Github, выполнив команды Azure PowerShell.

### Шаг 1. Поиск шаблона в JSON-файле

Далее приведено содержимое JSON-файла для шаблона.

	{
	"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
    "parameters": {
        "region": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "uniqueStorageAccountName"
        },
        "adminUsername": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "dnsNameforLBIP": {
            "type": "string",
            "defaultValue": "uniqueDnsNameforLBIP"
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM"
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB"
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP"
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "allowedValues": [
                "Standard_A0",
                "Standard_A1",
                "Standard_A2",
                "Standard_A3",
                "Standard_A4"
            ]
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('region')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('region')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('region')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('region')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE",
                        "properties": {
                            "backendIPConfigurations": [
                                {
                                    "id": "[variables('backEndIPConfigID1')]"
                                },
                                {
                                    "id": "[variables('backEndIPConfigID2')]"
                                }
                            ]
                        }
                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID1')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID2')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('region')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
	}


### Шаг 2. Создание развертывания с помощью шаблона

Введите имена развертывания Azure и группы ресурсов, а также расположение Azure и выполните следующие команды.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

При выполнении команды New-AzureResourceGroupDeployment вы увидите запрос на ввод значений для параметров JSON-файла. Если вы указали значения всех параметров, команда создаст группу ресурсов и развертывание.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Должно отобразиться примерно следующее:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

## <a id="updatevm"></a>ЗАДАЧА: обновление виртуальной машины, развернутой с использованием шаблона диспетчера ресурсов

Ниже приведен пример изменения файла шаблона JSON для обновления конфигурации виртуальной машины, развернутой с помощью шаблона диспетчера ресурсов. В этом примере вы создадите виртуальную машину Windows, а затем обновите ее, чтобы установить расширение Symantec Endpoint Protection.

### Шаг 1. Создание виртуальной машины с помощью шаблона

При необходимости создайте на компьютере папку для хранения файлов шаблона. Введите имя папки и выполните эти команды Azure PowerShell.

	$myFolder="<your folder path, such as C:\azure\templates\CreateVM>"
	$webClient=New-Object System.Net.WebClient
	$url="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	$filePath=$myFolder + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.parameters.json"
	$filePath = $myFolder + "\azuredeploy.parameters.json"
	$webclient.DownloadFile($url,$filePath)

Найдите в папке файл azuredeploy.parameters.json, откройте его в текстовом редакторе, укажите значения для четырех параметров, а затем сохраните файл.

Введите имя нового развертывания, имя новой группы ресурсов и расположение Azure, а затем выполните следующие команды.

	$deployName="<name for the new deployment>"
	$RGName="<name for the new Resource Group>"
	$locName="<an Azure location, such as West US>"
	cd $myFolder
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Вы увидите нечто вроде этого:

	PS C:\azure\templates\windowsvm> $deployName="winvmexttest"
	PS C:\azure\templates\windowsvm> $RGName="winvmexttest"
	PS C:\azure\templates\windowsvm> $locname="West US"
	PS C:\azure\templates\windowsvm> New-AzureResourceGroup -Name $RGName -Location $locName
	VERBOSE: 11:22:02 AM - Created resource group 'winvmexttest' in location 'westus'
	
	
	ResourceGroupName : winvmexttest
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *
	
	ResourceId        : /subscriptions/a58ce54a-c262-460f-b8ef-fe36e6d5f5ec/resourceGroups/winvmexttest

	PS C:\azure\templates\windowsvm> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -Template
	File azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 11:22:05 AM - Template is valid.
	VERBOSE: 11:22:05 AM - Create template deployment 'winvmexttest'.
	VERBOSE: 11:22:14 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is running
	VERBOSE: 11:22:37 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 11:22:39 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 11:22:41 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 11:22:43 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:22:52 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 11:26:36 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 6:26:38 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

После этого подключитесь к виртуальной машине на портале предварительной версии Azure (**Обзор > Виртуальные машины (v2) > ** *Имя виртуальной машины* **> Подключиться**).

На начальном экране введите **Symantec**. Обратите внимание, что компоненты Symantec Endpoint Protection не установлены (результаты поиска со словом «Symantec» в названии отсутствуют).

Закройте подключение к удаленному рабочему столу.

### Шаг 2. Изменение файла azuredeploy.json для добавления расширения Symantec Endpoint Protection

Откройте файл azuredeploy.json в любом текстовом редакторе. В разделе **переменных** добавьте сразу после строки, которая определяет переменную publicIPAddressType, такую строку:

	"vmExtensionName" : "SymantecExtension",

В разделе **ресурсов** добавьте перед строкой с последней левой квадратной скобкой (]) такой раздел:

	       {
	         "type": "Microsoft.Compute/virtualMachines/extensions",
	        "name": "[concat(variables('vmName'),'/', variables('vmExtensionName'))]",
	        "apiVersion": "2014-12-01-preview",
	        "location": "[variables('location')]",
	        "dependsOn": [
	            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
	        ],
	        "properties": {
	            "publisher": "Symantec",
	            "type": "SymantecEndpointProtection",
	            "typeHandlerVersion": "12.1",
	            "settings": null,
	            "protectedSettings": null
	        }
	    }

Сохраните файл azuredeploy.json с этими изменениями. Убедитесь, что изменения были внесены правильно, используя следующую команду.

	Test-AzureResourceGroupTemplate -ResourceGroupName $RGName -TemplateFile azuredeploy.json

Если изменения внесены правильно, вы увидите такой результат:

	Template is valid.

Если вместо этого сообщения появилось сообщение об ошибке, проанализируйте ее и определите причину.

### Шаг 3. Выполнение измененного шаблона для добавления расширения Symantec Endpoint Protection

В командной строке Azure PowerShell выполните такую команду:

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Вы увидите нечто вроде этого:

	PS C:\azure\templates\winvmext> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateF	ile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 12:49:42 PM - Template is valid.
	VERBOSE: 12:49:42 PM - Create template deployment 'winvmexttest'.
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 12:49:47 PM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 12:49:49 PM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 12:49:51 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 12:50:08 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	VERBOSE: 12:50:15 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension'	provisioning status is running
	VERBOSE: 12:53:07 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension' provisioning status is succeeded
	
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 7:53:07 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Подключитесь к виртуальной машине на портале предварительной версии Azure (**Обзор > Виртуальные машины (v2) > ** *Имя виртуальной машины* **> Подключиться**).

На начальном экране введите **Symantec**. На рисунке ниже показан возможный результат поискового запроса. Он свидетельствует о том, расширение Symantec Endpoint Protection установлено.

![](./media/virtual-machines-deploy-rmtemplates-powershell/SymantecExt.png)

## <a id="removerg"></a>ЗАДАЧА: удаление группы ресурсов

Вы можете удалить все созданные группы ресурсов с помощью команды **Remove-AzureResourceGroup**. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Remove-AzureResourceGroup  -Name "<resource group name>"

Вы увидите примерно такую информацию:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

## <a id="logon"></a>ЗАДАЧА: вход в виртуальную машину под управлением ОС Windows

На [портале предварительной версии Azure](https://portal.azure.com/) последовательно щелкните **Просмотреть все > Виртуальные машины (v2) >** *Имя виртуальной машины* **> Подключиться**.

Когда будет предложено открыть или сохранить файл RDP, нажмите кнопку **Открыть**, а затем нажмите кнопку **Подключиться**. Введите учетные данные действительной учетной записи и нажмите кнопку **ОК**.

Когда будет предложено подключиться, несмотря на ошибки сертификатов, нажмите кнопку **Да**.

## <a id="displayvm"></a>ЗАДАЧА: отображение информации о виртуальной машине

Информацию о виртуальной машине можно просмотреть, выполнив команду **Get-AzureVM**. Эта команда возвращает объект виртуальной машины, которым можно управлять, используя другие командлеты для обновления состояния виртуальной машины. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Вы увидите примерно такую информацию о виртуальной машине:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://buildsaacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines


## <a id="start"></a>ЗАДАЧА: запуск виртуальной машины

Вы можете запустить виртуальную машину, используя команду **Start-AzureVM**. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Start-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Вы увидите примерно такую информацию:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

## <a id="stop"></a>ЗАДАЧА: остановка виртуальной машины

Вы можете остановить виртуальную машину, используя команду **Stop-AzureVM**. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Stop-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Вы увидите примерно такую информацию:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

## <a id=restart"></a>ЗАДАЧА: перезапуск виртуальной машины

Вы можете перезапустить виртуальную машину, используя команду **Restart-AzureVM**. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Restart-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Вы увидите примерно такую информацию:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

## <a id=delete"></a>ЗАДАЧА: удаление виртуальной машины

Вы можете удалить виртуальную машину, используя команду **Remove-AzureVM**. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена. Чтобы пропустить запрос на подтверждение, используйте параметр **–Force**.

	Remove-AzureVM -ResourceGroupName "<resource group name>" –Name "<VM name>"

Вы увидите примерно такую информацию:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Обзор диспетчера ресурсов Azure](../resource-group-overview.md)

[Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Установка и настройка Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=July15_HO2-->