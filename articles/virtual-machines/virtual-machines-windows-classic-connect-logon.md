---
title: "Вход на классическую виртуальную машину Azure | Документация Майкрософт"
description: "Использование классического портала Azure для входа в виртуальную машину Windows, созданную с использованием классической модели развертывания."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f8fb116f14f771947f321f59d9880df4cdfe6859


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Вход в виртуальную машину под управлением Windows с помощью классического портала Azure
На классическом портале Azure для запуска сеанса удаленного рабочего стола и входа в виртуальную машину Windows используйте кнопку **Подключиться** .

Хотите подключиться к виртуальной машине Linux? Ознакомьтесь со статьей [How to log on to a virtual machine running Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Как войти в виртуальную машину под управлением Linux).

Узнайте, как [выполнить эти действия с помощью нового портала Azure](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Дополнительные сведения о том, как войти в виртуальную машину с помощью модели Resource Manager, см. [здесь](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Видеоруководство
Ниже приведено пошаговое описание действий в этом руководстве в формате видеоролика. В нем также описаны конечные точки, а также общие и частные порты, используемые для подключения к виртуальной машине Windows в Azure.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине
1. Войдите на классический портал Azure.
2. Щелкните **Виртуальные машины**и выберите виртуальную машину.
3. На панели команд в нижней части страницы щелкните **Подключиться**.
   
    ![Вход на виртуальную машину](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> Если кнопка **Подключиться** недоступна, ознакомьтесь с советами по устранению неполадок в конце этой статьи.
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>Вход на виртуальную машину
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Если кнопка **Подключиться** неактивна или возникли другие проблемы с подключением к удаленному рабочему столу, попробуйте переустановить конфигурацию. На панели мониторинга виртуальной машины в разделе **Сводка** щелкните **Reset remote configuration** (Сброс удаленной конфигурации).
* Если возникли проблемы с паролем, попробуйте сбросить его. На панели мониторинга виртуальной машины в разделе **Сводка** щелкните **Сбросить пароль**.

Если эти рекомендации не помогли устранить проблему или они вам не подходят, см. статью [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). В ней описывается процесс диагностики и решения распространенных проблем.




<!--HONumber=Dec16_HO1-->


