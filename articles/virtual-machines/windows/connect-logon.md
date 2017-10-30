---
title: "Подключение к виртуальной машине Windows Server | Документация Майкрософт"
description: "Узнайте, как подключиться к виртуальной машине Windows и войти на нее с помощью портала Azure и модели развертывания Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 2b748e4fc536a933dc41f9f673a534598b7343b8
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2017
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее
Чтобы запустить сеанс удаленного рабочего стола, на портале Azure нажмите кнопку **Подключиться** на рабочем столе Windows. Сначала вы подключитесь к виртуальной машине, а затем сможете войти на нее.

Если вы пытаетесь подключиться к виртуальной машине Windows с компьютера Mac, необходимо установить клиент RDP для Mac, например [Удаленный рабочий стол (Майкрософт)](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине
1. Перейдите на [портал Azure](https://portal.azure.com/), если вы еще этого не сделали.
2. В меню слева щелкните **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. На странице виртуальной машины щелкните **Подключить**.
   
    ![Снимок экрана портала Azure: подключение к виртуальной машине](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Если кнопка **Подключить** на портале неактивна и вы не используете канал [Express Route](../../expressroute/expressroute-introduction.md) или [VPN-подключение типа "сеть — сеть"](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) для обмена данными с Azure, вам сначала нужно создать общедоступный IP-адрес и назначить его виртуальной машине. Только после этого вы сможете использовать протокол RDP. Дополнительные сведения см. в статье [IP-адреса в Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Вход на виртуальную машину
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Дальнейшие действия
Если при попытке подключения возникает ошибка, см. сведения в статье, посвященной [устранению неполадок с подключением к удаленному рабочему столу](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). В ней описывается процесс диагностики и решения распространенных проблем.

