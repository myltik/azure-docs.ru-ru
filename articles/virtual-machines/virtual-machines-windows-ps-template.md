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
    ms.date="10/06/2016"
    ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов

В этой статье рассказывается о шаблоне Azure Resource Manager и демонстрируется использование PowerShell для его развертывания. Этот шаблон позволяет выполнить развертывание одной виртуальной машины под управлением Windows Server в новой виртуальной сети с одной подсетью.

Процедура, описанная в этой статье, занимает около 20 минут.

> [AZURE.IMPORTANT] Если вы хотите, чтобы виртуальная машина входила в группу доступности, добавьте ее в группу при создании. Добавлять виртуальные машины в группу доступности после создания пока невозможно.

## <a name="step-1:-create-the-template-file"></a>Шаг 1. Создание файла шаблона

Вы можете создать собственный шаблон на основе [шаблонов Azure Resource Manager](../resource-group-authoring-templates.md). Кроме того, можно развернуть шаблоны, которые были созданы для вас на основе [шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

1. Откройте текстовый редактор и добавьте необходимый элемент схемы и необходимый элемент contentVersion.

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Параметры](../resource-group-authoring-templates.md#parameters) требуются не всегда, но при развертывании шаблона с их помощью можно передавать значения. Добавьте элемент parameters и его дочерние элементы после элемента contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Переменные](../resource-group-authoring-templates.md#variables) в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров. Добавьте элемент variables после раздела parameters:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. [ресурсы](../resource-group-authoring-templates.md#resources) , например виртуальная машина, виртуальная сеть и учетная запись хранения. Добавьте раздел resources после раздела variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] В этой статье создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Сохраните файл шаблона как *VirtualMachineTemplate.json*.

## <a name="step-2:-create-the-parameters-file"></a>Шаг 2. Создание файла параметров

Чтобы задать значения для параметров ресурсов, которые были определены в шаблоне, создайте файл параметров, содержащий значения, используемые при развертывании шаблона.

1. В текстовом редакторе скопируйте эти данные JSON в новый файл с именем *Parameters.json*.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] См. дополнительные сведения о [требованиях к имени пользователя и паролю](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Сохраните файл параметров.

## <a name="step-3:-install-azure-powershell"></a>Шаг 3. Установка Azure PowerShell

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4:-create-a-resource-group"></a>Шаг 4. Создание группы ресурсов

Все ресурсы должны развертываться в [группе ресурсов](../resource-group-overview.md).

1. Получите список доступных расположений, где можно создавать ресурсы.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Замените значение **$locName** расположением из списка, например **Центральная часть США**. Создайте переменную.

        $locName = "location name"
        
3. Замените значение **$rgName** именем новой группы ресурсов. Создайте переменную и группу ресурсов.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Вы увидите нечто вроде этого примера:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5:-create-the-resources-with-the-template-and-parameters"></a>Шаг 5. Создание ресурсов с помощью шаблона и параметров

Замените значение **$templatePath** путем и именем файла шаблона. Замените значение **$parameterFile** путем и именем файла параметров. Создайте переменные, а затем разверните шаблон. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] Шаблоны и параметры можно также развернуть из учетной записи хранения Azure. Дополнительные сведения см. в статье [Использование Azure PowerShell со службой хранилища Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Дальнейшие действия

- При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении неполадок развертываний групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Узнайте, как управлять созданной виртуальной машиной, прочитав статью [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).



<!--HONumber=Oct16_HO2-->


