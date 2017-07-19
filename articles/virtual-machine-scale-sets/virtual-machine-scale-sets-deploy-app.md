---
title: "Развертывание приложения в наборах масштабирования виртуальных машин"
description: "Использование расширений для развертывания приложения в масштабируемом наборе виртуальных машин Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: e6a5e3a378a5661c09f770a202c10d270f324447
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017

---

# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Развертывание приложения в масштабируемых наборах виртуальных машин

В этой статье описываются различные способы установки программного обеспечения во время подготовки масштабируемого набора.

Возможно, вам потребуется обратиться к статье [Обзор проектов масштабируемых наборов](virtual-machine-scale-sets-design-overview.md), в которой описаны некоторые ограничения, налагаемые масштабируемыми наборами виртуальных машин.

## <a name="capture-and-reuse-an-image"></a>Запись и повторное использование образа

Виртуальную машину, созданную в Azure, можно использовать для подготовки базового образа для масштабируемого набора. Этот процесс создает управляемый диск в вашей учетной записи хранения, на который можно ссылаться как на базовый образ для масштабируемого набора. 

Сделайте следующее:

1. Создайте виртуальную машину Azure
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. Подключитесь к виртуальной машине и настройте необходимые параметры системы.

   Теперь вы можете установить приложение. Но учтите, что если вы установите приложение сейчас, обновить его будет сложнее, так как потребуется сначала удалить его. Вместо этого вы можете использовать этот шаг для установки необходимых компонентов, которые могут потребоваться приложению, таких как определенные компоненты среды выполнения или операционной системы.

3. Выполните процедуру в руководстве по "записи виртуальной машины" для [Linux][linux-vm-capture] или [Windows][windows-vm-capture].

4. Создайте [масштабируемый набор виртуальных машин][vmss-create], используя URI образа, записанного на предыдущем шаге.

Дополнительные сведения о дисках см. в статьях [Обзор управляемых дисков](../storage/storage-managed-disks-overview.md) и [Использование подключенных дисков данных](virtual-machine-scale-sets-attached-disks.md).

## <a name="install-when-the-scale-set-is-provisioned"></a>Установка при подготовке масштабируемого набора

Расширения виртуальных машин можно применить к масштабируемому набору виртуальных машин. Используя расширение виртуальной машины, можно настроить виртуальные машины в масштабируемом наборе как целую группу. Дополнительные сведения о расширениях см. в статье, посвященной [расширениям виртуальных машин](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Вы можете использовать три основных расширения в зависимости от типа операционной системы: Linux или Windows.

### <a name="windows"></a>Windows

Для операционной системы на основе Windows используйте расширение **Custom Script 1.8** или **PowerShell DSC**.

#### <a name="custom-script"></a>Custom Script

Расширение Custom Script запускает сценарий на каждом экземпляре виртуальной машины в масштабируемом наборе. В файле конфигурации или переменной указывается, какие файлы скачиваются на виртуальную машину и какие команды затем выполняются. Таким образом, вы можете запустить, например, программу установки, сценарий, пакетный файл или любой исполняемый файл.

PowerShell использует для параметров хэш-таблицу. В этом примере расширение Custom Script настраивается для запуска сценария PowerShell, который устанавливает службы IIS.

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Используйте параметр `-ProtectedSetting` для любых параметров, которые могут содержать конфиденциальные сведения.

---------


Интерфейс командной строки Azure использует для параметров JSON-файл. В этом примере расширение Custom Script настраивается для запуска сценария PowerShell, который устанавливает службы IIS. Сохраните следующий JSON-файл с именем _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

Затем выполните следующую команду Azure CLI.

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Используйте параметр `--protected-settings` для любых параметров, которые могут содержать конфиденциальные сведения.

### <a name="powershell-dsc"></a>PowerShell DSC

PowerShell DSC можно использовать для настройки экземпляров виртуальных машин в масштабируемом наборе. Расширение **DSC**, опубликованное **Microsoft.Powershell**, развертывает и запускает предоставленную конфигурацию DSC для каждого экземпляра виртуальной машины. Файл конфигурации или переменная указывает расширению, где находится *ZIP*-пакет и какую комбинацию _сценария и функции_ использовать.

PowerShell использует для параметров хэш-таблицу. В этом примере развертывается пакет DSC, который устанавливает службы IIS.

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Используйте параметр `-ProtectedSetting` для любых параметров, которые могут содержать конфиденциальные сведения.

-----------

Интерфейс командной строки Azure использует для параметров JSON-файл. В этом примере развертывается пакет DSC, который устанавливает службы IIS. Сохраните следующий JSON-файл с именем _settings.json_.

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

Затем выполните следующую команду Azure CLI.

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Используйте параметр `--protected-settings` для любых параметров, которые могут содержать конфиденциальные сведения.

### <a name="linux"></a>Linux

Linux может использовать расширение **Custom Script 2.0** или пакет **cloud-init** во время создания.

Custom Script представляет собой простое расширение, которое загружает файлы в экземпляры виртуальной машины и выполняет команду.

#### <a name="custom-script"></a>Custom Script

Сохраните следующий JSON-файл с именем _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Используйте Azure CLI для добавления этого расширения в существующий масштабируемый набор виртуальных машин. Каждая виртуальная машина в масштабируемом наборе автоматически запускает расширение.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Используйте параметр `--protected-settings` для любых параметров, которые могут содержать конфиденциальные сведения.

#### <a name="cloud-init"></a>Cloud-Init

Cloud-Init используется при создании масштабируемого набора. Во-первых, создайте локальный файл с именем _cloud-init.txt_ и добавьте в него свою конфигурацию. Например, ознакомьтесь с [этой статьей](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt).

Используйте интерфейс командной строки Azure для создания масштабируемого набора. В поле `--custom-data` можно ввести имя файла сценария cloud-init.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>Как управлять обновлениями приложений?

При развертывании приложения с помощью расширения измените определение расширения каким-либо образом. Это изменение вызовет повторное развертывание расширения для всех экземпляров виртуальных машин. В расширение **необходимо** внести какие-либо изменения, например переименовать указанный файл, в противном случае Azure не определит расширение как измененное.

Если приложение встроено в образ операционной системы, используйте для обновления приложения конвейер автоматизированного развертывания. Спроектируйте архитектуру таким образом, чтобы упростить быстрый ввод промежуточного масштабируемого набора в рабочую среду. Хорошим примером этого подхода является [работа драйвера Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

[Packer](https://www.packer.io/) и [Terraform](https://www.terraform.io/) поддерживают Azure Resource Manager, поэтому можно определить свои образы в виде кода, создать их в Azure и затем использовать виртуальный жесткий диск в масштабируемом наборе. Но такой подход не подходит для образов Marketplace, в которых расширения и пользовательские сценарии более важны, так как вы не оперируете данными из Marketplace напрямую.

## <a name="what-happens-when-a-scale-set-scales-out"></a>Что происходит при расширении масштабируемого набора?
При добавлении одной или нескольких виртуальных машин в масштабируемый набор приложение устанавливается автоматически. Например, если в масштабируемом наборе определены расширения, то они будут запускаться на каждой новой виртуальной машине при ее создании. Если масштабируемый набор основан на исходном пользовательском образе, то любая новая виртуальная машина является его копией. Если виртуальные машины в масштабируемом наборе являются узлами контейнера, возможно, потребуется реализовать загрузку контейнеров кодом запуска в расширение Custom Script. Кроме того, расширение может устанавливать агент, который регистрируется в оркестраторе кластера, например в службе контейнеров Azure.


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Как можно развернуть обновление ОС в доменах обновления?
Предположим, вам требуется обновить образ ОС, не прерывая работу масштабируемого набора виртуальных машин. PowerShell и Azure CLI могут обновлять образы виртуальных машин, по одной виртуальной машине за раз. В статье [Обновление набора масштабирования виртуальных машин](./virtual-machine-scale-sets-upgrade-scale-set.md) также содержатся сведения о том, какие варианты обновления операционной системы доступны в масштабируемом наборе виртуальных машин.

## <a name="next-steps"></a>Дальнейшие действия

* [Управление масштабируемым набором с помощью PowerShell](virtual-machine-scale-sets-windows-manage.md)
* [Создание шаблона масштабируемого набора](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md


