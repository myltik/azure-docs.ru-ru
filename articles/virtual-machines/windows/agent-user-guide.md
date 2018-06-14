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
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: a5da5cfb6cda940f68171e42d4512b304fc5f114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915679"
---
# <a name="azure-virtual-machine-agent-overview"></a>Обзор агента виртуальной машины Azure

Агент виртуальной машины Microsoft Azure — это защищенный упрощенный процесс, который управляет взаимодействием виртуальной машины с контроллером структуры Azure. Основная роль агента виртуальной машины — это включение и выполнение расширений виртуальной машины Azure. Расширения виртуальной машины дают возможность выполнить дополнительные действия по настройке виртуальных машин после развертывания, например установить и настроить программное обеспечение. Также они предоставляют возможности восстановления, такие как сброс пароля администратора виртуальной машины. Расширения виртуальной машины не могут выполняться без агента виртуальной машины Azure.

В этом документе описаны процессы установки, обнаружения и удаления агента виртуальной машины Azure.

## <a name="install-the-vm-agent"></a>"Установить агент виртуальной машины"

### <a name="azure-gallery-image"></a>Образ из коллекции Azure

По умолчанию агент виртуальной машины Azure устанавливается на любой виртуальной машине Windows, развернутой с помощью образа из коллекции Azure. При развертывании образа из коллекции Azure с помощью портала, PowerShell, интерфейса командной строки или шаблона Azure Resource Manager также устанавливается агент виртуальной машины. 

### <a name="manual-installation"></a>Ручная установка

Агент виртуальной машины Windows можно установить вручную с помощью пакета установщика Windows. Ручная установка может потребоваться при создании пользовательского образа виртуальной машины, который будет развернут в Azure. Чтобы установить агент виртуальной машины Windows вручную, скачайте установщик агента виртуальной машины из этого расположения: [Скачать агент виртуальной машины Azure для Windows](http://go.microsoft.com/fwlink/?LinkID=394789). 

Чтобы установить агент виртуальной машины, дважды щелкните файл установщика Windows. Для автоматической установки агента виртуальной машины выполните следующую команду:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Обнаружение агента виртуальной машины

### <a name="powershell"></a>PowerShell

Для получения сведений о виртуальных машинах Azure можно воспользоваться модулем PowerShell Azure Resource Manager. Выполнение команды `Get-AzureRmVM` возвращает довольно много сведений, включая состояние подготовки для агента виртуальной машины Azure.

```PowerShell
Get-AzureRmVM
```

Ниже приводится только часть выходных данных `Get-AzureRmVM`. Обратите внимание на свойство `ProvisionVMAgent`, входящее в `OSProfile`. Это свойство можно использовать, чтобы определить, развернут ли на виртуальной машине агент виртуальной машины.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Следующий сценарий можно использовать для получения краткого списка имен виртуальных машин и состояния агента виртуальной машины.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Обнаружение вручную

После входа в виртуальную машину Azure под управлением Windows для проверки запущенных процессов можно использовать диспетчер задач. Чтобы проверить состояние агента виртуальной машины Azure, откройте диспетчер задач, перейдите на вкладку "Подробности" и найдите процесс с именем `WindowsAzureGuestAgent.exe`. Наличие этого процесса означает, что агент виртуальной машины установлен.

## <a name="upgrade-the-vm-agent"></a>Установка агента виртуальной машины

Агент виртуальной машины Azure для Windows обновляется автоматически. Так как новые виртуальные машины развертываются в Azure, на них устанавливается последняя версия агента виртуальной машины. Чтобы на пользовательских образах виртуальных машин была новая версия агента виртуальной машины, их необходимо обновлять вручную.
