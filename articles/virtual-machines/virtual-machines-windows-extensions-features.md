---
title: "Расширения и компоненты виртуальной машины | Документация Майкрософт"
description: "Узнайте о расширениях, доступных для виртуальных машин Azure и сгруппированных по предоставляемым функциям или улучшениям."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 7bd48e2ef9177369190494c38bfdcf1bb99e61ea


---
# <a name="about-virtual-machine-extensions-and-features"></a>Обзор расширений и компонентов виртуальной машины
## <a name="azure-vm-extensions"></a>Расширения виртуальной машины Azure
Расширения виртуальной машины Azure — это небольшие приложения, выполняющие настройку и автоматизацию после развертывания на виртуальных машинах Azure. Например, если на виртуальной машине нужно установить программное обеспечение, антивирусную защиту или конфигурацию Docker, это можно сделать с помощью расширения виртуальной машины. Расширения виртуальной машины Azure можно запускать с помощью интерфейса командной строки Azure, PowerShell, шаблонов Resource Manager и портала Azure. Расширения могут предоставляться в рамках нового развертывания виртуальной машины и работать в любой из существующих систем.

В этом документе указаны необходимые компоненты для установки расширений виртуальной машины Azure, а также рекомендации по поиску доступных расширений. 

## <a name="azure-vm-agent"></a>Агент виртуальной машины Azure
Агент виртуальной машины Azure управляет взаимодействием виртуальной машины с контроллером структуры Azure. Агент виртуальной машины отвечает за многие функциональные аспекты развертывания виртуальных машин Azure и управления ими, включая запуск расширений виртуальной машины. Агент ВМ Azure предварительно установлен в образах из коллекции Azure. Также его можно установить в поддерживаемых операционных системах. 

Сведения о поддерживаемых операционных системах и инструкции по установке см. в статье [Руководство пользователя агента Linux для Azure](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="discover-vm-extensions"></a>Знакомство с расширениями виртуальной машины
Существует множество различных расширений виртуальной машины, которые можно использовать с виртуальными машинами Azure. Чтобы просмотреть полный список, выполните следующую команду с помощью интерфейса командной строки Azure, подставив нужное расположение.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Распространенные расширения виртуальной машины
| Имя расширения | Описание | Дополнительные сведения |
| --- | --- | --- |
| Расширение Custom Script в ОС Windows |Выполняет скрипты на виртуальной машине Azure |[Расширение Custom Script в ОС Windows](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Расширение DSC в ОС Windows |Расширение PowerShell DSC (настройка требуемого состояния). |[Расширение виртуальной машины Docker](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Расширение системы диагностики Azure |Управляет системой диагностики Azure |[Расширение системы диагностики Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |




<!--HONumber=Nov16_HO3-->


