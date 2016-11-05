---
title: Создание виртуальной машины под управлением Windows на классическом портале | Microsoft Docs
description: Информация о создании виртуальной машины под управлением Windows на классическом портале Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Создание виртуальной машины под управлением Windows на классическом портале Azure
> [!div class="op_single_selector"]
> * [Классический портал Azure](virtual-machines-windows-classic-tutorial.md)
> * [PowerShell: классическое развертывание](virtual-machines-windows-classic-create-powershell.md)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Узнайте, как [выполнить эти действия для модели развертывания с помощью Resource Manager](virtual-machines-windows-hero-tutorial.md). Если вы собираетесь использовать новый портал Azure, см. статью [Создание первой виртуальной машины Windows на портале Azure](virtual-machines-windows-hero-tutorial.md).

В этом учебнике показано, как просто создать виртуальную машину Azure под управлением Windows на классическом портале Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов Windows.

В этом руководстве рассматривается использование метода **Из коллекции** на классическом портале Azure для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание** . Например, при необходимости присоединить виртуальную машину к виртуальной сети следует использовать параметр **Из коллекции** .

Можно также создавать виртуальные машины с помощью [собственных образов](virtual-machines-windows-classic-createupload-vhd.md). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](virtual-machines-windows-creation-choices.md).

## <a name="video-walkthrough"></a>Видеоруководство
Ниже приведено видео с пошаговым выполнением действий по этому учебнику.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a name="<a-id="createvirtualmachine">-</a>create-the-virtual-machine"></a><a id="createvirtualmachine"> </a>Создание виртуальной машины
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Войдите в виртуальную машину. Инструкции см. в статье [Вход в виртуальную машину под управлением Windows Server](virtual-machines-windows-classic-connect-logon.md).
* Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Инструкции см. в статье [Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания](virtual-machines-windows-classic-attach-disk.md).

<!--HONumber=Oct16_HO2-->


