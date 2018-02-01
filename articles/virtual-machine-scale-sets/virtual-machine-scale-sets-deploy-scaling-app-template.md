---
title: "Создание масштабируемого набора виртуальных машин с помощью шаблона Azure | Документация Майкрософт"
description: "Узнайте, как быстро создать масштабируемый набор виртуальных машин с помощью шаблона Azure Resource Manager"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: 201b752c2a79362f2e049d2e0f0b953d77aaedfe
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Создание масштабируемого набора виртуальных машин с помощью Azure CLI 2.0
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Вы можете вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. В этом руководстве по началу работы описано, как создадать масштабируемый набор виртуальных машин с помощью шаблона Azure Resource Manager. Вы также можете создать масштабируемый набор с помощью [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) или [портала Azure](virtual-machine-scale-sets-create-portal.md).


## <a name="overview-of-templates"></a>Общие сведения о шаблонах
Шаблоны Azure Resource Manager позволяют развертывать группы связанных ресурсов. Шаблоны написаны в формате JSON (нотация объектов JavaScript) и определяют всю среду инфраструктуры Azure для приложения. С помощью одного шаблона можно создать масштабируемый набор виртуальных машин, установить приложения и настроить правила автоматического масштабирования. Вы можете повторно использовать этот шаблон, применив переменные и параметры, чтобы обновить существующие или создать дополнительные масштабируемые наборы. Шаблоны можно развернуть с помощью портала Azure, Azure CLI 2.0 или Azure PowerShell либо вызывать их из конвейеров непрерывной интеграции и непрерывной поставки (CI/CD).

Дополнительные сведения о шаблонах см. в [обзоре Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).


## <a name="define-a-scale-set"></a>Определение масштабируемого набора
Шаблон определяет конфигурацию для каждого типа ресурсов. Тип ресурса для масштабируемого набора виртуальных машин тот же, что и для отдельной виртуальной машины. Ниже перечислены основные элементы и типы ресурсов для масштабируемого набора виртуальных машин.

| Свойство                     | Описание свойства                                  | Пример значения в шаблоне                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Тип                         | Создаваемый тип ресурса Azure                            | Microsoft.Compute/virtualMachineScaleSets; |
| name                         | Имя масштабируемого набора                                       | myScaleSet                                |
| location                     | Расположение для создания масштабируемого набора                     | Восток США                                   |
| sku.name                     | Размер виртуальной машины для каждого экземпляра в масштабируемом наборе                  | Standard_A1                               |
| sku.capacity                 | Количество экземпляров виртуальных машин, которое требуется изначально создать           | 2                                         |
| upgradePolicy.mode           | Режим обновления экземпляра виртуальной машины при внесении изменений              | Автоматический                                 |
| imageReference               | Платформа или пользовательский образ, используемые для экземпляров виртуальных машин | Сервер Canonical Ubuntu 16.04-LTS         |
| osProfile.computerNamePrefix | Префикс имени для каждого экземпляра виртуальной машины                     | myvmss                                    |
| osProfile.adminUsername      | Имя пользователя для каждого экземпляра виртуальной машины                        | azureuser                                 |
| osProfile.adminPassword      | Пароль для каждого экземпляра виртуальной машины                        | P@ssw0rd!                                 |

 Во фрагменте кода ниже показано определение основного ресурса масштабируемого набора в шаблоне. Для краткости в примере не показана конфигурация адаптера виртуальной сети. Чтобы настроить шаблон масштабируемого набора, вы можете изменить размер виртуальной машины или начальную емкость либо использовать другую платформу или пользовательский образ.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
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
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
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


## <a name="install-an-application"></a>Установка приложения
Когда вы развертываете масштабируемый набор, расширения виртуальных машин могут предусматривать задачи автоматизации и настройки после развертывания, например задачи установки приложения. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Чтобы применить расширение к масштабируемому набору, добавьте раздел *extensionProfile* в предыдущий пример ресурса. Как правило, профиль расширения определяет следующие свойства:

- тип расширения;
- издатель расширения;
- версия расширения;
- расположение скриптов настройки или установки;
- команды для выполнения на экземплярах виртуальных машин.

Рассмотрим два способа установки приложения с расширениями: настраиваемое расширение скриптов для установки приложения Python в среде Linux или расширение PowerShell DSC для установки приложения ASP.NET в Windows.

### <a name="python-http-server-on-linux"></a>HTTP-сервер Python под управлением Linux
[HTTP-сервер Python под управлением Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) использует настраиваемое расширение скриптов для установки [Bottle](http://bottlepy.org/docs/dev/), веб-платформы Python и простого HTTP-сервера. 

Два скрипта определены в файлах *fileUris* - *installserver.sh* и *workserver.py*. Эти файлы загружаются из GitHub, а затем *commandToExecute* определяет `bash installserver.sh` для приложения, которое будет установлено и настроено:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```

### <a name="aspnet-application-on-windows"></a>Приложение ASP.NET для Windows
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
Самый простой способ развернуть шаблон [HTTP-сервера Python под управлением Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) или шаблон [приложения ASP.NET MVC под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) — использовать кнопку **Развертывание в Azure**, расположенную в файлах сведений на портале GitHub.  Чтобы развернуть примеры шаблонов, можно также использовать PowerShell или Azure CLI.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Для установки HTTP-сервера Python в Linux можно использовать Azure CLI 2.0:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Чтобы просмотреть, как работает приложение, получите общедоступный IP-адрес подсистемы балансировки нагрузки с помощью команды [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show):

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Введите в браузер общедоступный IP-адрес своей подсистемы балансировки нагрузки в формате *http://<publicIpAddress>:9000/do_work*. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Веб-страница NGINX по умолчанию](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
Для установки приложения ASP.NET в Windows можно использовать Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

Чтобы изучить работу приложения, получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), как показано ниже.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Введите в браузер общедоступный IP-адрес своей подсистемы балансировки нагрузки в формате *http://<publicIpAddress>/MyApp*. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Выполнение сайта IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ненужную группу ресурсов, масштабируемый набор и все связанные ресурсы с помощью команды [az group delete](/cli/azure/group#az_group_delete), как показано ниже.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Дополнительная информация
