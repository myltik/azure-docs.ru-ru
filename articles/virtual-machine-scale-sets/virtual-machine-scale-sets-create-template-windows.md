---
title: Создание масштабируемого набора виртуальных машин Windows с помощью шаблона Azure | Документация Майкрософт
description: Узнайте, как быстро создать масштабируемый набор виртуальных машин Windows с помощью шаблона Azure Resource Manager, который позволяет развернуть пример приложения и настроить правила автоматического масштабирования.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 1632411b0cfc2f8fa59f323436ee386e763a1ae0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201076"
---
# <a name="create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Создание масштабируемого набора виртуальных машин Windows с помощью шаблона Azure
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Вы можете вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. В этом руководстве по началу работы описано, как создадать масштабируемый набор виртуальных машин с помощью шаблона Azure Resource Manager. Также можно создать масштабируемый набор с помощью [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) или [портала Azure](virtual-machine-scale-sets-create-portal.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль Azure PowerShell 4.4.1 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.


## <a name="define-a-scale-set-in-a-template"></a>Определение масштабируемого набора в шаблоне
Шаблоны Azure Resource Manager позволяют развертывать группы связанных ресурсов. Шаблоны написаны в формате JSON (нотация объектов JavaScript) и определяют всю среду инфраструктуры Azure для приложения. С помощью одного шаблона можно создать масштабируемый набор виртуальных машин, установить приложения и настроить правила автоматического масштабирования. Вы можете повторно использовать этот шаблон, применив переменные и параметры, чтобы обновить существующие или создать дополнительные масштабируемые наборы. Шаблоны можно развернуть с помощью портала Azure, Azure CLI 2.0 или Azure PowerShell либо же на основе конвейеров непрерывной интеграции и непрерывной поставки (CI/CD).

Дополнительные сведения о шаблонах см. в [обзоре Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Шаблон определяет конфигурацию для каждого типа ресурсов. Тип ресурса для масштабируемого набора виртуальных машин тот же, что и для отдельной виртуальной машины. Ниже перечислены основные элементы и типы ресурсов для масштабируемого набора виртуальных машин.

| Свойство                     | Описание свойства                                  | Пример значения в шаблоне                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Тип                         | Создаваемый тип ресурса Azure                            | Microsoft.Compute/virtualMachineScaleSets; |
| name                         | Имя масштабируемого набора                                       | myScaleSet                                |
| location                     | Расположение для создания масштабируемого набора                     | Восток США                                   |
| sku.name                     | Размер виртуальной машины для каждого экземпляра в масштабируемом наборе                  | Standard_A1                               |
| sku.capacity                 | Количество экземпляров виртуальных машин, которое требуется изначально создать           | 2                                         |
| upgradePolicy.mode           | Режим обновления экземпляра виртуальной машины при внесении изменений              | Автоматический                                 |
| imageReference               | Платформа или пользовательский образ, используемые для экземпляров виртуальных машин. | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Префикс имени для каждого экземпляра виртуальной машины                     | myvmss                                    |
| osProfile.adminUsername      | Имя пользователя для каждого экземпляра виртуальной машины                        | azureuser                                 |
| osProfile.adminPassword      | Пароль для каждого экземпляра виртуальной машины                        | P@ssw0rd!                                 |

 В примере ниже показано определение основных ресурсов для масштабируемого набора. Чтобы настроить шаблон масштабируемого набора, вы можете изменить размер виртуальной машины или начальную емкость либо же использовать другую платформу или пользовательский образ.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Для краткости в примере опущена конфигурация адаптера виртуальной сети. Также не показаны дополнительные компоненты, такие как подсистема балансировки нагрузки. Полный шаблон масштабируемого набора приведен [в конце этой статьи](#deploy-the-template).


## <a name="install-an-application"></a>Установка приложения
Когда вы развертываете масштабируемый набор, расширения виртуальных машин могут предусматривать задачи автоматизации и настройки после развертывания, например задачи установки приложения. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Чтобы применить расширение к масштабируемому набору, добавьте раздел *extensionProfile* в предыдущий пример ресурса. Как правило, профиль расширения определяет следующие свойства:

- тип расширения;
- издатель расширения;
- версия расширения;
- расположение скриптов настройки или установки;
- команды для выполнения на экземплярах виртуальных машин.

В примере шаблона [приложения ASP.NET в Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) используется расширение PowerShell DSC для установки приложения ASP.NET MVC, выполняющегося в IIS. 

Скрипт установки скачивается с сайта GitHub, как определено в свойстве *url*. Затем расширение запускает *InstallIIS* из скрипта *IISInstall.ps1*, как определено в свойствах *function* и *script*. Само приложение ASP.NET предоставляется в виде пакета веб-развертывания, который также скачивается с сайта GitHub, как определено в свойстве *WebDeployPackagePath*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Развертывание шаблона
Шаблон [приложения ASP.NET MVC в Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) можно развернуть с помощью кнопки **Развертывание в Azure**. После ее нажатия откроется портал Azure, загрузится весь шаблон и будет предложено ввести некоторые параметры, такие как имя масштабируемого набора, число экземпляров и учетные данные администратора.

[![Развертывание шаблона в Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Приложение ASP.NET в Windows можно также установить с помощью Azure PowerShell, используя командлет [New AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment), следующим образом:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

По запросу укажите имя масштабируемого набора и учетные данные администратора для экземпляров виртуальных машин. Чтобы создать масштабируемый набор и применить расширение для настройки приложения, может потребоваться 10–15 минут.


## <a name="test-your-sample-application"></a>Проверка примера приложения
Чтобы изучить работу приложения, получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), как показано ниже.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Введите в браузере общедоступный IP-адрес своей подсистемы балансировки нагрузки в формате *http://publicIpAddress/MyApp*. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Выполнение сайта IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ненужную группу ресурсов, масштабируемый набор и все связанные ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), как показано ниже:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="next-steps"></a>Дополнительная информация
В этой статье по началу работы вы создали масштабируемый набор Windows с помощью шаблона Azure и использовали расширение PowerShell DSC, чтобы установить простое приложение ASP.NET на экземплярах виртуальных машин. Чтобы улучшить масштабируемость и автоматизацию, расширьте возможности масштабируемого набора, следуя инструкциям в следующих статьях:

- [Развертывание приложения в масштабируемых наборах виртуальных машин](virtual-machine-scale-sets-deploy-app.md)
- Автоматическое масштабирование с помощью [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) или [портала Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Автоматические обновления ОС масштабируемого набора виртуальных машин Azure](virtual-machine-scale-sets-automatic-upgrade.md)
