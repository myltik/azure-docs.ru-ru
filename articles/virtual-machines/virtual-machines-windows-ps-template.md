<properties
	pageTitle="Создание виртуальной машины с помощью шаблона диспетчера ресурсов | Microsoft Azure"
	description="Простое создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов

В этой статье рассказывается о шаблоне Azure Resource Manager и демонстрируется использование PowerShell для его развертывания. Этот шаблон позволяет выполнить развертывание одной виртуальной машины под управлением Windows Server в новой виртуальной сети с одной подсетью.

Процедура, описанная в этой статье, занимает около 20 минут.

## Шаг 1. Создание файла шаблона

Вы можете создать собственный шаблон на основе [шаблонов Azure Resource Manager](../resource-group-authoring-templates.md). Кроме того, можно развернуть шаблоны, которые были созданы для вас на основе [шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/). Пример, используемый в этой статье, похож на шаблон, описанный в статье [Deploy a simple Windows VM in West US](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows в западной части США).

1. Откройте текстовый редактор и скопируйте эти данные JSON в новый файл с именем *VirtualMachineTemplate.json*:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
                    "sku" : "[variables('windowsOSVersion')]",
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
        
    >[AZURE.NOTE] В этой статье создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Navigate and select Linux virtual machine images in Azure with CLI or PowerShell](virtual-machines-linux-cli-ps-findimage.md).
    
2. Сохраните файл шаблона.

## Шаг 2. Создание файла параметров

Чтобы задать значения для параметров ресурсов, которые были определены в шаблоне, вы создаете файл параметров, содержащий значения, и передаете его в диспетчер ресурсов с шаблоном.

1. В текстовом редакторе скопируйте эти данные JSON в новый файл с именем *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

4. Сохраните файл параметров.

## Шаг 3. Установка Azure PowerShell

Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и выполнить вход в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Шаг 4. Создание группы ресурсов

Все ресурсы должны развертываться в группе ресурсов. Дополнительные сведения см. в статье [Обзор Azure Resource Manager](../resource-group-overview.md).

1. Получите список доступных расположений, где можно создавать ресурсы.

	    Get-AzureLocation | sort Name | Select Name

2. Замените значение **$locName** расположением из списка, например **Центральная часть США**. Создайте переменную.

        $locName = "location name"
        
3. Замените значение **$rgName** именем новой группы ресурсов. Создайте переменную и группу ресурсов.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Вы увидите нечто вроде этого:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

### Шаг 7. Создание ресурсов с помощью шаблона и параметров

1. Замените значение **$deployName** именем развертывания. Замените значение **$templatePath** путем и именем файла шаблона. Замените значение **$templatePath** путем и именем файла параметров. Создайте переменные. 

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

4. Разверните шаблон.

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    Должно отобразиться примерно следующее:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] Шаблоны и параметры можно также развернуть из учетной записи хранения Azure. Дополнительные сведения см. в статье [Использование Azure PowerShell со службой хранилища Azure](../storage-powershell-guide-full.md).

## Дальнейшие действия

- При наличии проблем с развертыванием ознакомьтесь со статьей [Устранение неполадок развертываний групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Узнайте, как управлять созданной виртуальной машиной, ознакомившись со статьей [Управление виртуальными машинами с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0420_2016-->