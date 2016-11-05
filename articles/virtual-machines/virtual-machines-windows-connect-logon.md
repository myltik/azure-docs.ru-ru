---
title: Подключение к виртуальной машине Windows Server | Microsoft Docs
description: Узнайте, как подключиться к виртуальной машине Windows и войти на нее с помощью портала Azure и модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2016
ms.author: cynthn

---
# Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее
Чтобы запустить сеанс удаленного рабочего стола, на портале Azure нажмите кнопку **Подключиться**. Сначала вы подключитесь к виртуальной машине, а затем сможете войти на нее.

## Подключение к виртуальной машине
1. Перейдите на [портал Azure](https://portal.azure.com/), если вы еще этого не сделали.
2. В главном меню щелкните **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. В колонке виртуальной машины щелкните **Подключить**.
   
    ![Снимок экрана портала Azure: подключение к виртуальной машине](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > Если кнопка **Подключить** на портале неактивна и вы не подключены к Azure с помощью канала [Express Route](../expressroute/expressroute-introduction.md) или VPN-подключения типа [сеть — сеть](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), вам нужно сначала создать виртуальную машину и назначить ей общедоступный IP-адрес. Только после этого вы сможете использовать протокол RDP. Дополнительные сведения см. в статье [IP-адреса в Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## Вход на виртуальную машину
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Дальнейшие действия
Если при попытке подключения возникает ошибка, см. сведения в статье [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). В ней описывается процесс диагностики и решения распространенных проблем.

<!---HONumber=AcomDC_0803_2016-->