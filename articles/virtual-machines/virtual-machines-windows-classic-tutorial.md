---
title: "Создание виртуальной машины на классическом портале | Документация Майкрософт"
description: "Информация о создании виртуальной машины под управлением Windows на классическом портале Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ede78ff35ec6e7007e7a1c7b946c8e17bd425f37


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Создание виртуальной машины под управлением Windows на классическом портале Azure
> [!div class="op_single_selector"]
> * [Классический портал Azure](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell: классическое развертывание](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br>

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия для модели развертывания Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя **новый портал Azure**. 

В этом учебнике показано, как создать виртуальную машину Azure под управлением Windows на классическом портале Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов Windows.

В этом руководстве рассматривается использование метода **Из коллекции** на классическом портале Azure для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание** . Например, при необходимости присоединить виртуальную машину к виртуальной сети следует использовать параметр **Из коллекции** .

Можно также создавать виртуальные машины с помощью [собственных образов](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Видеоруководство
Ниже приведено видео с пошаговым выполнением действий по этому учебнику.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a id="createvirtualmachine"> </a>Создание виртуальной машины
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [создать виртуальную машину для модели развертывания с помощью Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя новый портал Azure. 
* Войдите в виртуальную машину. Инструкции см. в статье [Вход в виртуальную машину под управлением Windows Server](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Инструкции см. в статье [Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Feb17_HO3-->


