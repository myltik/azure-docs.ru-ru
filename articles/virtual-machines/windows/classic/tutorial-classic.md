---
title: Создание виртуальной машины на портале Azure | Документация Майкрософт
description: Информация о создании виртуальной машины под управлением Windows на портале Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5fd2128ff436d3211f41c7dfdcc4c2b8aabd0eb0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Создание виртуальной машины под управлением Windows на портале Azure
> [!div class="op_single_selector"]
> * [портал Azure](tutorial.md)
> * [PowerShell: классическое развертывание](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия для модели развертывания с помощью Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя **портал Azure**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

В этом руководстве показано, как создать виртуальную машину Azure под управлением Windows на портале Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов Windows.

В этом разделе рассматривается использование **панели мониторинга** на портале Azure для выбора и создания виртуальной машины.

Можно также создавать виртуальные машины с помощью [собственных образов](createupload-vhd.md). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"> </a>Создание виртуальной машины
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [создать виртуальную машину для модели развертывания с помощью Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), используя портал Azure.
* Войдите в виртуальную машину. Инструкции см. в статье [Вход в виртуальную машину под управлением Windows Server](connect-logon.md).
* Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Инструкции см. в статье [Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания](attach-disk.md).
