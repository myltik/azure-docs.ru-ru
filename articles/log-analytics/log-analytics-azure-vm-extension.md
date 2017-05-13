---
title: "Подключение виртуальных машин Azure к службе Log Analytics | Документация Майкрософт"
description: "Для виртуальных машин Windows и Linux, работающих в Azure, рекомендуемым способом сбора данных журналов и метрик является установка расширения виртуальной машины Azure Log Analytics. Для установки расширения виртуальной машины Log Analytics на виртуальные машины Azure можно использовать портал Azure или PowerShell."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: richrund
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 1cab9d2f814e0c36dadcdd7bbc3cdc736de0af49
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Подключение виртуальных машин Azure к Log Analytics с помощью агента Log Analytics

Для компьютеров Windows и Linux рекомендуемым методом сбора данных журналов и метрик является установка агента Log Analytics.

Проще всего установить агент Log Analytics на виртуальные машины Azure при помощи расширения виртуальной машины Log Analytics.  Использование расширения упрощает процесс установки и автоматически настраивает агент на отправку данных в указанную вами рабочую область службы Log Analytics. Кроме того, агент обновляется автоматически, обеспечивая наличие новейших компонентов и исправлений.

Для виртуальных машин Windows необходимо включить расширение виртуальной машины *Microsoft Monitoring Agent*.
Для виртуальных машин Linux необходимо включить расширение виртуальной машины *Агент OMS для Linux*.

Подробнее о [расширениях виртуальной машины Azure](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) и [агенте для Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

При использовании агента для сбора данных журнала необходимо настроить [источники данных в Log Analytics](log-analytics-data-sources.md), указав, какие данные журналов и метрик следует собирать.

> [!IMPORTANT]
> Если настроить службу Log Analytics для индексации данных журналов с помощью [системы диагностики Azure](log-analytics-azure-storage.md), а агент — для сбора данных тех же журналов, то данные журналов будут собираться дважды. Плата взимается за использование обоих источников данных. Если агент уже установлен, то сбор данных журнала должен выполняться только с помощью агента. Настраивать Log Analytics для сбора данных журнала системы диагностики Azure не требуется.
>
>

Существует три простых способа включить расширение виртуальной машины Log Analytics:

* с помощью портала Azure;
* с помощью Azure PowerShell;
* с помощью шаблона Azure Resource Manager.

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Включение расширения виртуальной машины на портале Azure
Установить агент для Log Analytics и подключить виртуальную машину Azure для его использования можно с помощью [портала Azure](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Установка агента Log Analytics и подключение виртуальной машины к рабочей области Log Analytics
1. Войдите на [портал Azure](http://portal.azure.com).
2. Выберите **Обзор** в левой части страницы, затем найдите пункт **Log Analytics (OMS)** и выберите его.
3. В списке рабочих областей Log Analytics выберите ту, которую вы собираетесь использовать для работы с виртуальной машиной Azure.  
   ![Рабочие области OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. В меню **Управление службой Log Analytics** выберите плитку **Виртуальные машины**.  
   ![Виртуальные машины](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. В списке **Виртуальные машины** выберите виртуальную машину, на которую следует установить агент. Для параметра **состояния подключения OMS** выбранной виртуальной машины указано значение **Не подключено**.  
   ![Виртуальная машина не подключена](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. В колонке сведений о выбранной виртуальной машине выберите **Подключить**. Агент будет автоматически установлен и настроен для рабочей области Log Analytics. Этот процесс занимает несколько минут. В течение этого времени отображается следующее состояние подключения OMS: *Подключение…*  
   ![Подключение виртуальной машины](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. После установки и подключения агента состояние **подключения OMS** обновится и примет значение **Эта рабочая область**.  
   ![Подключено](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>Включение расширения виртуальной машины с помощью PowerShell
Настраивая виртуальную машину с помощью PowerShell, вам нужно указать **идентификатор** и **ключ** рабочей области. Для имен свойств в конфигурации JSON следует **учитывать регистр**.

Идентификатор и ключ можно найти на странице **Параметры** портала OMS или с помощью PowerShell, как показано в предыдущем примере.

![Идентификатор рабочей области и первичный ключ](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Существуют разные команды для классических виртуальных машин Azure и виртуальных машин, созданных с помощью Resource Manager. Ниже представлено несколько примеров для виртуальных машин обоих типов.

Для классических виртуальных машин используйте следующий пример команды PowerShell:

```PowerShell
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Для виртуальных машин Linux, созданных с помощью Resource Manager, используйте следующую команду интерфейса командной строки:
```azurecli
az vm extension set --resource-group myRGMonitor --vm-name myMonitorVM --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --version 1.3 --protected-settings ‘{"workspaceKey": "<workspace-key>"}’ --settings ‘{"workspaceId": "<workspace-id>"}’ 
```

Для моделей виртуальных машин, созданных с помощью Resource Manager, используйте следующий пример команды PowerShell:

```PowerShell
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```


## <a name="deploy-the-vm-extension-using-a-template"></a>Развертывание расширения виртуальной машины с помощью шаблона
С помощью Azure Resource Manager можно создать шаблон (в формате JSON), определяющий развертывание и конфигурацию приложения. Этот шаблон, который также называется шаблоном диспетчера ресурсов, предоставляет декларативный способ определения развертывания. Такой шаблон позволяет повторно развертывать приложение в течение его жизненного цикла и гарантирует согласованное развертывание ресурсов.

Включив агент Log Analytics в шаблон Resource Manager, можно обеспечить предварительную настройку каждой виртуальной машины для отправки отчетов в рабочую область Log Analytics.

Дополнительную информацию о шаблонах Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Ниже приведен пример шаблона Resource Manager, который используется для развертывания виртуальной машины Windows с установленным расширением Microsoft Monitoring Agent. По сути это стандартный шаблон виртуальной машины с некоторыми дополнениями:

* Параметры workspaceId и workspaceName.
* Раздел расширения ресурса Microsoft.EnterpriseCloud.Monitoring.
* Выходные данные выполняют поиск workspaceId и workspaceSharedKey.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
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
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
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
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
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
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
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
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
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
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
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
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Развернуть шаблон можно с помощью следующей команды PowerShell:

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Устранение неполадок с расширением Log Analytics для виртуальной машины
Если что-то не работает должным образом, как правило, вы должны получить сообщение, отправленное либо порталом Azure, либо средствами Azure PowerShell.

1. Войдите на [портал Azure](http://portal.azure.com).
2. Найдите виртуальную машину и откройте сведения о ней.
3. Щелкните **Расширения**, чтобы проверить, включено ли расширение OMS.

   ![Представление расширения виртуальной машины](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. Щелкните расширение *MicrosoftMonitoringAgent*(Windows) или *OmsAgentForLinux*(Linux), чтобы просмотреть подробные сведения. 

   ![Сведения о расширении виртуальной машины](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Устранение неполадок на виртуальных машинах Windows
Если расширение агента виртуальной машины *Microsoft Monitoring Agent* не устанавливается или не создает отчеты, можно выполнить следующие действия для решения проблемы.

1. Следуя инструкции в статье базы знаний [KB 2965986](https://support.microsoft.com/kb/2965986#mt1), убедитесь, что агент виртуальной машины установлен и работает правильно.
   * Также можно проверить, существует ли файл журнала агента ВМ `C:\WindowsAzure\logs\WaAppAgent.log`.
   * Если журнал не создан, значит агент виртуальной машины не установлен.
     * [Установите агент виртуальной машины Azure на классической виртуальной машине](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. Проверьте пульс расширения Microsoft Monitoring Agent, выполнив следующие действия:
   * Войдите на виртуальную машину.
   * Откройте планировщик задач и найдите задачу `update_azureoperationalinsight_agent_heartbeat`.
   * Убедитесь, что задача активна и выполняется ежеминутно.
   * Проверьте, есть ли в папке `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` файл журнала пульса.
3. Просмотрите файлы журнала расширения виртуальной машины Microsoft Monitoring Agent в папке `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`.
4. Убедитесь, что на виртуальной машине можно запускать сценарии PowerShell.
5. Убедитесь, что разрешения на доступ к папке C:\Windows\temp не были изменены.
6. Просмотрите состояние агента Microsoft Monitoring Agent. Для этого на виртуальной машине в окне PowerShell с повышенными правами введите следующую команду: `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Просмотрите файлы журнала установки агента Microsoft Monitoring Agent в папке `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`.

Подробные сведения см. в статье об [устранении неполадок расширений для виртуальных машин Windows](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="troubleshooting-linux-virtual-machines"></a>Устранение неполадок на виртуальных машинах Linux
Если расширение агента виртуальной машины *Агент OMS для Linux* не устанавливается или не создает отчеты, можно выполнить следующие действия для решения проблемы.

1. Если состояние расширения *неизвестно*, убедитесь, что агент виртуальной машины Azure установлен и работает правильно, просмотрев файл журнала агента ВМ `/var/log/waagent.log`.
   * Если журнал не создан, значит агент виртуальной машины не установлен.
   * [Установите агент виртуальной машины Azure на виртуальной машине Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
2. В случае возникновения других неполадок просмотрите файлы журнала расширения ВМ "Агент OMS для Linux" в папке `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` и `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`.
3. Если расширение исправно, но данные не отправляются, просмотрите файлы журнала агента OMS для Linux в папке `/var/opt/microsoft/omsagent/log/omsagent.log`.

Подробные сведения см. в статье [Устранение неполадок расширения виртуальной машины Linux](../virtual-machines/linux/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Дальнейшие действия
* Настройте [источники данных в Log Analytics](log-analytics-data-sources.md) чтобы указать, какие данные журналов и метрик следует собирать.
* Для сбора данных с виртуальных машин [добавьте решения Log Analytics из коллекции решений](log-analytics-add-solutions.md).
* [С помощью системы диагностики Azure соберите данные](log-analytics-azure-storage.md) для других ресурсов, работающих в Azure.

На компьютеры, не подключенные к Azure, агент Log Analytics можно установить при помощи методов, описанных в следующих статьях:

* [Подключение компьютеров Windows к Log Analytics](log-analytics-windows-agents.md)
* [Подключение компьютеров Linux к Log Analytics](log-analytics-linux-agents.md)

