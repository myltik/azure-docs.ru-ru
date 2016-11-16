---
title: "Подключение к виртуальной машине Windows Server | Документация Майкрософт"
description: "Узнайте, как подключиться к виртуальной машине Windows и войти на нее с помощью портала Azure и модели развертывания Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db417fb72442ea8a5cd4ef882eb657b08bebaa0a


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее
Чтобы запустить сеанс удаленного рабочего стола, на портале Azure нажмите кнопку **Подключиться** . Сначала вы подключитесь к виртуальной машине, а затем сможете войти на нее.

## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине
1. Перейдите на [портал Azure](https://portal.azure.com/), если вы еще этого не сделали.
2. В главном меню щелкните **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. В колонке виртуальной машины щелкните **Подключить**.
   
    ![Снимок экрана портала Azure: подключение к виртуальной машине](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > Если кнопка **Подключить** на портале неактивна и вы не используете канал [Express Route](../expressroute/expressroute-introduction.md) или [VPN-подключение типа "сеть — сеть"](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) для обмена данными с Azure, вам сначала нужно создать общедоступный IP-адрес и назначить его виртуальной машине. Только после этого вы сможете использовать протокол RDP. Дополнительные сведения см. в статье [IP-адреса в Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Вход на виртуальную машину
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Дальнейшие действия
Если при попытке подключения возникает ошибка, см. сведения в статье, посвященной [устранению неполадок с подключением к удаленному рабочему столу](virtual-machines-windows-troubleshoot-rdp-connection.md). В ней описывается процесс диагностики и решения распространенных проблем.




<!--HONumber=Nov16_HO2-->


