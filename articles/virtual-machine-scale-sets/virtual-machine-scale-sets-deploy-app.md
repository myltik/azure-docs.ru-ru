---
title: "Развертывание приложения в масштабируемый набор виртуальных машин Azure | Документация Майкрософт"
description: "Узнайте, как развертывать приложения в экземплярах виртуальных машин Windows и Linux, размещенных в масштабируемом наборе."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 7e03d5e2bbdb1b3b206fa7fa455f7dce7951f02b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Развертывание приложения в масштабируемых наборах виртуальных машин
Для запуска приложений в экземплярах виртуальных машин в масштабируемом наборе необходимо сначала установить компоненты и необходимые файлы этих приложений. В этой статье описано, как создать настраиваемый образ виртуальной машины для экземпляров в масштабируемом наборе или автоматически запустить сценарии установки на существующих экземплярах виртуальных машин. Вы также узнаете, как управлять приложением или обновлениями ОС в масштабируемом наборе.


## <a name="build-a-custom-vm-image"></a>Создание настраиваемого образа виртуальной машины
При использовании одного из образов платформы Azure для создания экземпляров в масштабируемом наборе не выполняется установка или настройка какого-либо дополнительного программного обеспечения. Можно автоматизировать установку этих компонентов, хотя это увеличит время подготовки экземпляров виртуальных машин для масштабируемого набора. Если внести много изменений в конфигурацию экземпляров виртуальных машин, это увеличит число операций управления, связанных с этими сценариями и задачами настройки.

Чтобы уменьшить число операций управления конфигурацией и время подготовки виртуальной машины, можно создать настраиваемый образ виртуальной машины, который позволит подготавливать в масштабируемом наборе экземпляры, уже готовые к запуску вашего приложения. Общий процесс создания настраиваемого образа виртуальной машины для экземпляров масштабируемого набора выглядят следующим образом.

1. Чтобы создать настраиваемый образ виртуальной машины для экземпляров масштабируемого набора, нужно создать виртуальную машину, войти в нее, а затем установить и настроить приложение. Можно использовать Packer для определения и создания образа виртуальной машины [Linux](../virtual-machines/linux/build-image-with-packer.md) или [Windows](../virtual-machines/windows/build-image-with-packer.md). Можно также создать и настроить виртуальную машину вручную:

    - Можно создать виртуальную машину Linux с помощью [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md) или [портала](../virtual-machines/linux/quick-create-portal.md).
    - Можно создать виртуальную машину Windows с помощью [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), [Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md) или [портала](../virtual-machines/windows/quick-create-portal.md).
    - Войдите на виртуальную машину [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) или [Windows](../virtual-machines/windows/connect-logon.md).
    - Установите и настройте необходимые приложения и инструменты. Если требуются определенные версии библиотеки или среды выполнения, их можно определить в настраиваемом образе виртуальной машины. 

2. Запишите образ виртуальной машины с помощью [Azure CLI 2.0](../virtual-machines/linux/capture-image.md) или [Azure PowerShell](../virtual-machines/windows/capture-image.md). Будет создан настраиваемый образ виртуальной машины для последующего развертывания экземпляров в масштабируемом наборе.

3. [Создайте масштабируемый набор](virtual-machine-scale-sets-create.md) и укажите настраиваемый образ виртуальной машины, созданный на предыдущем шаге.


## <a name="already-provisioned"></a>Установка приложения с помощью расширения настраиваемых сценариев
Расширение настраиваемых сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения.

Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager, а также его можно запустить с помощью интерфейса командной строки Azure, PowerShell, портала Azure или API REST виртуальной машины Azure. 

Дополнительные сведения см. в статье [Расширение Custom Script в ОС Windows](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Использование Azure PowerShell
PowerShell использует хэш-таблицу для хранения скачиваемых файлов и выполняемых команд. В следующем примере выполняются следующие действия:

- Указывает экземплярам виртуальной машины скачать сценарий GitHub: *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Настраивает расширение для запуска сценария установки: `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`.
- Возвращает сведения о масштабируемом наборе с помощью командлета [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss).
- Применяет расширение к экземплярам виртуальных машин с помощью командлета [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

Расширение настраиваемых сценариев применяется к экземплярам виртуальных машин набора *myScaleSet* в группе ресурсов *myResourceGroup*. Введите свои имена следующим образом.

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Если для масштабируемого набора задана политика обновления *вручную*, обновите экземпляры виртуальных машин с помощью командлета [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Этот командлет применит обновленную конфигурацию масштабируемого набора к экземплярам виртуальных машин и установит ваше приложение.


### <a name="use-azure-cli-20"></a>Использование Azure CLI 2.0
Чтобы использовать расширение настраиваемых сценариев с Azure CLI, создайте JSON-файл, который определяет получаемые файлы и выполняемые команды. Эти определения JSON можно многократно использовать в крупномасштабных развертываниях масштабируемых наборов, чтобы обеспечить согласованную установку приложений.

В текущей оболочке создайте файл *customConfig.json* и вставьте в него следующую конфигурацию. Например, создайте файл в Cloud Shell, не на локальном компьютере. Вы можете использовать любой редактор. Введите `sensible-editor cloudConfig.json`, чтобы создать файл и просмотреть список доступных редакторов.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Примените конфигурацию расширения настраиваемых сценариев к экземплярам виртуальных машин в своем масштабируемом наборе командой [az vmss extension set](/cli/azure/vmss/extension#set). Следующий пример применяет конфигурацию *customConfig.json* к экземплярам виртуальных машин набора *myScaleSet* в группе ресурсов *myResourceGroup*. Введите свои имена следующим образом.

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Если для масштабируемого набора задана политика обновления *вручную*, обновите экземпляры виртуальных машин с помощью команды [az vmss update-instances](/cli/azure/vmss#update-instances). Этот командлет применит обновленную конфигурацию масштабируемого набора к экземплярам виртуальных машин и установит ваше приложение.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Установка приложения на виртуальную машину Windows с помощью PowerShell DSC
[PowerShell Desired State Configuration](https://msdn.microsoft.com/en-us/powershell/dsc/overview) — это платформа управления, которая позволяет определить конфигурацию целевых виртуальных машин. Конфигурации DSC определяют компоненты, которые следует установить на виртуальной машине, а также параметры настройки узла. На каждом целевом узле, который обрабатывает запрашиваемые действия на основе отправленных конфигураций, выполняется модуль локального диспетчера конфигураций (LCM).

Расширение PowerShell DSC позволяет настроить экземпляры виртуальных машин в масштабируемом наборе с помощью PowerShell. В следующем примере выполняются следующие действия:

- Указывает экземплярам виртуальных машин скачать пакет DSC из GitHub: *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Настраивает расширение для запуска сценария установки: `configure-http.ps1`.
- Возвращает сведения о масштабируемом наборе с помощью командлета [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss).
- Применяет расширение к экземплярам виртуальных машин с помощью командлета [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

Расширение DSC применяется к экземплярам виртуальных машин набора *myScaleSet* в группе ресурсов *myResourceGroup*. Введите свои имена следующим образом.

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
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

Чтобы создать масштабируемый набор и использовать файл cloud-init, добавьте параметр `--custom-data` в команду [az vmss create](/cli/azure/vmss#create) и укажите имя файла cloud-init. Следующий пример создает масштабируемый набор *myScaleSet* в группе ресурсов *myResourceGroup* и настраивает экземпляры виртуальных машин, используя файл *cloud-init.txt*. Введите свои имена следующим образом.

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


## <a name="install-applications-as-a-set-scales-out"></a>Установка приложений при развертывании набора масштабирования
Масштабируемые наборы позволяют увеличивать количество экземпляров виртуальных машин, на которых работает приложение. Этот процесс развертывания можно запустить вручную или автоматически, на основании метрик, например метрик использования ЦП или памяти.

Если к масштабируемому набору применяется расширение настраиваемых сценариев, то приложение устанавливается на каждый новый экземпляр виртуальной машины. Если масштабируемый набор основан на настраиваемом образе с предварительно установленным приложением, то каждый новый экземпляр виртуальной машины развертывается в рабочем состоянии. 

Если экземпляры виртуальных машины масштабируемого набора являются узлами контейнеров, то можно использовать расширение настраиваемых сценариев, чтобы извлекать и запускать необходимые образы контейнеров. Расширение настраиваемых сценариев также может зарегистрировать новый экземпляр виртуальной машины в оркестраторе, например в службе контейнеров Azure.


## <a name="deploy-application-updates"></a>Развертывание обновлений приложения
В случае обновления кода, библиотек или пакетов приложения можно передать последнее состояние приложения на экземпляры виртуальных машин в масштабируемом наборе. При использовании расширения настраиваемых сценариев обновления приложения не развертываются автоматически. Измените конфигурацию расширения настраиваемых сценариев, например, указав сценарий установки с именем обновленной версии. В предыдущем примере расширение настраиваемых сценариев использует сценарий *automate_nginx.sh* следующим образом.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Любые изменения, внесенные в приложение, не будут учтены в расширении настраиваемых сценариев, пока не будет изменен этот сценарий установки. Один из подходов заключается в том, чтобы добавить номер версии, который увеличивается по мере выпуска новых версий приложения. Теперь расширение настраиваемых сценариев сможет ссылаться на *automate_nginx_v2.sh* следующим образом.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Расширение настраиваемых сценариев будет запущено на экземплярах виртуальных машин для применения обновлений приложения.


### <a name="install-applications-with-os-updates"></a>Установка приложений с обновлениями ОС
Если доступны новые выпуски ОС, можно использовать имеющийся или создать новый настраиваемый образ и [развернуть обновления ОС](virtual-machine-scale-sets-upgrade-scale-set.md) в масштабируемом наборе. Каждый экземпляр виртуальной машины будет обновлен до последней версии указанного образа. Можно использовать настраиваемый образ с предварительно установленным приложением, расширение настраиваемых сценариев или PowerShell DSC, чтобы автоматически предоставить свое приложение при выполнении обновления. Может потребоваться спланировать обслуживание приложения при выполнении этого процесса, чтобы избежать проблем совместимости версий.

При использовании настраиваемого образа виртуальной машины с предварительно установленным приложением обновления приложения можно интегрировать в конвейер развертывания, чтобы создавать новые образы и развертывать обновления ОС в масштабируемом наборе. Такой подход позволяет конвейеру извлечь последние сборки приложения, создать и проверить образ виртуальной машины, а затем обновить экземпляры виртуальных машин в масштабируемом наборе. Чтобы запустить конвейер развертывания, который выполняет сборку обновлений приложения и развертывает их в настраиваемых образах виртуальных машин, можно использовать [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/) или [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Дальнейшие действия
Сведения о создании и развертывании приложений в масштабируемых наборах вы можете получить из раздела [Рекомендации по проектированию масштабируемых наборов](virtual-machine-scale-sets-design-overview.md). Дополнительные сведения об управлении масштабируемым набором см. в разделе [Управление масштабируемым набором виртуальных машин с помощью Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).
