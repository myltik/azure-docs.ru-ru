---
title: Развертывание приложения в масштабируемый набор виртуальных машин Azure | Документация Майкрософт
description: Узнайте, как развертывать приложения в экземплярах виртуальных машин Windows и Linux, размещенных в масштабируемом наборе.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: cadd0f4c07b7e8adec4956543f67313aa8442da3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Развертывание приложения в масштабируемых наборах виртуальных машин
Для запуска приложений в экземплярах виртуальных машин в масштабируемом наборе необходимо сначала установить компоненты и необходимые файлы этих приложений. В этой статье описано, как создать настраиваемый образ виртуальной машины для экземпляров в масштабируемом наборе или автоматически запустить сценарии установки на существующих экземплярах виртуальных машин. Вы также узнаете, как управлять приложением или обновлениями ОС в масштабируемом наборе.


## <a name="build-a-custom-vm-image"></a>Создание настраиваемого образа виртуальной машины
При использовании одного из образов платформы Azure для создания экземпляров в масштабируемом наборе не выполняется установка или настройка какого-либо дополнительного программного обеспечения. Можно автоматизировать установку этих компонентов, хотя это увеличит время подготовки экземпляров виртуальных машин для масштабируемого набора. Если внести много изменений в конфигурацию экземпляров виртуальных машин, это увеличит число операций управления, связанных с этими сценариями и задачами настройки.

Чтобы уменьшить число операций управления конфигурацией и время подготовки виртуальной машины, можно создать настраиваемый образ виртуальной машины, который позволит подготавливать в масштабируемом наборе экземпляры, уже готовые к запуску вашего приложения. Дополнительные сведения о том, как создать и использовать настраиваемый образ виртуальной машины с масштабируемым набором см. в документах ниже:

- [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Установка приложения с помощью расширения настраиваемых сценариев
Расширение настраиваемых сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Дополнительные сведения о том, как создать и использовать настраиваемый образ виртуальной машины с масштабируемым набором см. в документах ниже:

- [Azure CLI 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Установка приложения на виртуальную машину Windows с помощью PowerShell DSC
[PowerShell Desired State Configuration](https://msdn.microsoft.com/en-us/powershell/dsc/overview) — это платформа управления, которая позволяет определить конфигурацию целевых виртуальных машин. Конфигурации DSC определяют компоненты, которые следует установить на виртуальной машине, а также параметры настройки узла. На каждом целевом узле, который обрабатывает запрашиваемые действия на основе отправленных конфигураций, выполняется модуль локального диспетчера конфигураций (LCM).

Расширение PowerShell DSC позволяет настроить экземпляры виртуальных машин в масштабируемом наборе с помощью PowerShell. В следующем примере выполняются следующие действия:

- Указывает, что экземпляры виртуальной машины должны загрузить пакет DSC с GitHub: *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Настраивает расширение для запуска сценария установки: `configure-http.ps1`.
- Возвращает сведения о масштабируемом наборе с помощью командлета [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss).
- Применяет расширение к экземплярам виртуальных машин с помощью командлета [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

Расширение DSC применяется к экземплярам виртуальных машин набора *myScaleSet* в группе ресурсов *myResourceGroup*. Введите свои имена следующим образом.

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Если для масштабируемого набора задана политика обновления *вручную*, обновите экземпляры виртуальных машин с помощью командлета [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Этот командлет применит обновленную конфигурацию масштабируемого набора к экземплярам виртуальных машин и установит ваше приложение.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Установка приложения на виртуальную машину Linux с помощью cloud-init
[Пакет cloud-init](https://cloudinit.readthedocs.io/latest/) — широко используемое средство, используемое для настройки виртуальной машины Linux при ее первой загрузке. Вы можете использовать cloud-init для установки пакетов, записи файлов или настройки пользователей и параметров безопасности. Так как cloud-init выполняется при начальной загрузке, для применения вашей конфигурации не требуются какие-либо дополнительные действия или обязательные агенты.

Кроме того, cloud-init работает с разными дистрибутивами. Например, для установки пакета не используется **apt-get install** или **yum install**. Вместо этого можно определить список пакетов для установки. Файл cloud-init автоматически использует собственный инструмент управления пакетами из выбранного дистрибутива.

Дополнительные сведения, включая пример файла *cloud-init.txt*, доступны в разделе [Настройка виртуальной машины Linux в Azure с помощью cloud-init](../virtual-machines/linux/using-cloud-init.md).

Чтобы создать масштабируемый набор и использовать файл cloud-init, добавьте параметр `--custom-data` в команду [az vmss create](/cli/azure/vmss#az_vmss_create) и укажите имя файла cloud-init. Следующий пример создает масштабируемый набор *myScaleSet* в группе ресурсов *myResourceGroup* и настраивает экземпляры виртуальных машин, используя файл *cloud-init.txt*. Введите свои имена следующим образом.

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Установка приложений с обновлениями ОС
Если доступны новые выпуски ОС, можно использовать имеющийся или создать новый настраиваемый образ и [развернуть обновления ОС](virtual-machine-scale-sets-upgrade-scale-set.md) в масштабируемом наборе. Каждый экземпляр виртуальной машины будет обновлен до последней версии указанного образа. Можно использовать настраиваемый образ с предварительно установленным приложением, расширение настраиваемых сценариев или PowerShell DSC, чтобы автоматически предоставить свое приложение при выполнении обновления. Может потребоваться спланировать обслуживание приложения при выполнении этого процесса, чтобы избежать проблем совместимости версий.

При использовании настраиваемого образа виртуальной машины с предварительно установленным приложением обновления приложения можно интегрировать в конвейер развертывания, чтобы создавать новые образы и развертывать обновления ОС в масштабируемом наборе. Такой подход позволяет конвейеру извлечь последние сборки приложения, создать и проверить образ виртуальной машины, а затем обновить экземпляры виртуальных машин в масштабируемом наборе. Чтобы запустить конвейер развертывания, который выполняет сборку обновлений приложения и развертывает их в настраиваемых образах виртуальных машин, можно использовать [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/) или [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Дополнительная информация
Сведения о создании и развертывании приложений в масштабируемых наборах вы можете получить из раздела [Рекомендации по проектированию масштабируемых наборов](virtual-machine-scale-sets-design-overview.md). Дополнительные сведения об управлении масштабируемым набором см. в разделе [Управление масштабируемым набором виртуальных машин с помощью Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).
