---
title: "Об образах виртуальных машин Windows | Документация Майкрософт"
description: "Узнайте о том, как использовать образы с виртуальными машинами Windows в Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 91080c3d6f05bbafa80b5b14d2ce840b3432110d
ms.lasthandoff: 03/22/2017


---
# <a name="about-images-for-windows-virtual-machines"></a>Образы виртуальных машин
> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Дополнительные сведения о применении и поиске образов в модели Resource Manager см. [здесь](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Работа с образами

Для управления образами, доступными в вашей подписке Azure, можно использовать модуль Azure PowerShell и портал Azure. Модуль Azure PowerShell предоставляет дополнительные параметры команд, поэтому можно точно определить, что нужно просмотреть или сделать. Портал Azure предоставляет графический пользовательский интерфейс для выполнения множества повседневных задач администрирования.

Ниже приведены некоторые примеры использования модуля Azure PowerShell.

* **Получить все образы.** `Get-AzureVMImage` возвращает список всех образов, доступных в вашей текущей подписке (ваши образы и образы, предоставляемые Azure или партнерами). Итоговый список может быть большим. В следующих примерах показано, как сократить список.
* **Получить семейства образов.** `Get-AzureVMImage | select ImageFamily` возвращает список семейств образов, отображая свойство строк **ImageFamily**.
* **Получить все образы из указанного семейства.** `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Найти образы виртуальных машин:** `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` Этот командлет проводит фильтрацию по свойству DataDiskConfiguration, которое применяется только к образам виртуальных машин. В этом примере выходные данные также фильтруются, и выводятся только метка и имя образа.
* **Сохранить обобщенный образ.** `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Сохранить специализированный образ.** `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > Параметр OSState является обязательным для создания образа виртуальной машины, содержащего диск операционной системы и подключенные диски данных. Если не указать этот параметр, командлет создает образ ОС. Значение параметра указывает, является ли образ обобщенным или специальным и основывается ли он на диске операционной системы, подготовленном для повторного использования.

* **Удалить образ.** `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Дальнейшие действия
Вы можете [создать виртуальную машину Windows с помощью портала Azure](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

