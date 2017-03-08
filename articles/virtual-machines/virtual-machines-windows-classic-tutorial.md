---
title: "Создание виртуальной машины на портале Azure | Документация Майкрософт"
description: "Информация о создании виртуальной машины под управлением Windows на портале Azure."
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
ms.date: 02/27/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 15e6eb0fcd3baf34c2144aa3ce13a8aff59f9a5a
ms.openlocfilehash: 502c420b927ab835e585e848f153328d9f9565ee
ms.lasthandoff: 03/01/2017


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Создание виртуальной машины под управлением Windows на портале Azure
> [!div class="op_single_selector"]
> * [классический портал Azure](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell: классическое развертывание](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
>
>

<br>

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия для модели развертывания с помощью Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя **портал Azure**.

В этом руководстве показано, как создать виртуальную машину Azure под управлением Windows на портале Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов Windows.

В этом разделе рассматривается использование **панели мониторинга** на портале Azure для выбора и создания виртуальной машины.

Можно также создавать виртуальные машины с помощью [собственных образов](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<!-- 02/27/2017 Video removed as it was based on the classic portal. -->

## <a id="createvirtualmachine"> </a>Создание виртуальной машины
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [создать виртуальную машину для модели развертывания с помощью Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя портал Azure.
* Войдите в виртуальную машину. Инструкции см. в статье [Вход в виртуальную машину под управлением Windows Server](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Инструкции см. в статье [Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

