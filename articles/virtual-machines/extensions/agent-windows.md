---
title: Обзор агента виртуальной машины Azure | Документация Майкрософт
description: Обзор агента виртуальной машины Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: fb29f0f931715b8a6ba5b4528294eb61ef5762c8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944746"
---
# <a name="azure-virtual-machine-agent-overview"></a>Обзор агента виртуальной машины Azure
Агент виртуальной машины Microsoft Azure — это защищенный упрощенный процесс, который управляет взаимодействием виртуальной машины с контроллером структуры Azure. Основная роль агента виртуальной машины — это включение и выполнение расширений виртуальной машины Azure. Расширения виртуальной машины позволяют выполнять дополнительные действия по настройке виртуальной машины после развертывания, например устанавливать и настраивать программное обеспечение. Они также предоставляют возможности восстановления, такие как сброс пароля администратора виртуальной машины. Расширения виртуальной машины не могут выполняться без агента виртуальной машины Azure.

В этой статье описаны процессы установки, обнаружения и удаления агента виртуальной машины Azure.

## <a name="install-the-vm-agent"></a>"Установить агент виртуальной машины"

### <a name="azure-marketplace-image"></a>Образ из Azure Marketplace

Агент виртуальной машины Azure устанавливается по умолчанию на любой виртуальной машине Windows, развернутой с помощью образа из Azure Marketplace. При развертывании образа из Azure Marketplace с помощью портала, PowerShell, интерфейса командной строки или шаблона Azure Resource Manager также устанавливается агент виртуальной машины.

Пакет гостевого агента Windows состоит из двух частей:

- агент подготовки (PA);
- гостевой агент Windows (WinGA).

Для загрузки виртуальной машины на ней должен быть установлен агент подготовки. Устанавливать WinGA не требуется. Во время развертывания виртуальной машины можно выбрать вариант без установки WinGA. В следующем примере показано, как выбрать параметр *provisionVmAgent* с помощью шаблона Azure Resource Manager.

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Если агенты не установлены, вы не сможете использовать некоторые службы Azure, такие как Azure Backup или Azure Security. Для работы этих служб требуется установить расширение. Если виртуальная машина была развернута без WinGA, установить последнюю версию агента можно позднее.

### <a name="manual-installation"></a>Ручная установка
Агент виртуальной машины Windows можно установить вручную с помощью пакета установщика Windows. Установка вручную может потребоваться при создании пользовательского образа виртуальной машины, который будет развернут в Azure. Чтобы вручную установить агент виртуальной машины Windows, [скачайте установщик агента виртуальной машины](http://go.microsoft.com/fwlink/?LinkID=394789).

Чтобы установить агент виртуальной машины, дважды щелкните файл установщика Windows. Для автоматической установки агента виртуальной машины выполните следующую команду:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Обнаружение агента виртуальной машины

### <a name="powershell"></a>PowerShell

Для получения сведений о виртуальных машинах Azure можно воспользоваться модулем PowerShell Azure Resource Manager. Чтобы просмотреть сведения о виртуальной машине, например состояние подготовки агента виртуальной машины, выполните командлет [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm):

```powershell` Get-AzureRmVM
```

The following condensed example output shows the the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Следующий скрипт можно использовать для получения краткого списка имен виртуальных машин и состояния агента виртуальной машины.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Обнаружение вручную
После входа в виртуальную машину Azure под управлением Windows для проверки запущенных процессов можно использовать диспетчер задач. Чтобы обнаружить агент виртуальной машины Azure, откройте диспетчер задач, перейдите на вкладку *Подробности* и найдите процесс с именем **WindowsAzureGuestAgent.exe**. Наличие этого процесса означает, что агент виртуальной машины установлен.


## <a name="upgrade-the-vm-agent"></a>Установка агента виртуальной машины
Агент виртуальной машины Azure для Windows обновляется автоматически. Так как новые виртуальные машины развертываются в Azure, во время подготовки виртуальной машины на них устанавливается последняя версия агента виртуальной машины. Чтобы во время создания образа на пользовательских образах виртуальных машин устанавливалась новая версия агента виртуальной машины, их необходимо обновлять вручную.


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о расширениях виртуальных машин см. в статье c [обзором расширений и компонентов виртуальной машины Azure](overview.md).