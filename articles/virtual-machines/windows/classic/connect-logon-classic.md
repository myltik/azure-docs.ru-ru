---
title: Вход на классическую виртуальную машину Azure | Документация Майкрософт
description: Используйте портал Azure для входа в виртуальную машину Windows, созданную с использованием классической модели развертывания.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: bcbebc216dbd63acfb33cf72ba774d088149a3a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Вход в виртуальную машину под управлением Windows с помощью портала Azure
На портале Azure для запуска сеанса удаленного рабочего стола и входа в виртуальную машину Windows используйте кнопку **Подключиться** .

Хотите подключиться к виртуальной машине Linux? Ознакомьтесь со статьей [How to log on to a virtual machine running Linux](../../linux/mac-create-ssh-keys.md) (Как войти в виртуальную машину под управлением Linux).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Дополнительные сведения о том, как войти в виртуальную машину с помощью модели Resource Manager, см. [здесь](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине
1. Войдите на портал Azure.
2. Щелкните виртуальную машину, доступ к которой необходимо получить. Имя указывается в области **Все ресурсы**.

    ![Расположения виртуальных машин](./media/connect-logon/azureportaldashboard.png)

3. Щелкните **Подключиться** на панели команд вверху панели мониторинга виртуальной машины.

    ![Значок подключения для виртуальной машины](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Вход на виртуальную машину
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Дополнительная информация
* Если кнопка **Подключиться** неактивна или возникли другие проблемы с подключением к удаленному рабочему столу, попробуйте переустановить конфигурацию. Щелкните **Сброс удаленного доступа** на панели мониторинга виртуальной машины.

    ![Сброс удаленного доступа](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Если возникли проблемы с паролем, попробуйте сбросить его. Щелкните **Сбросить пароль** возле левого края панели мониторинга виртуальной машины, в разделе **Поддержка и устранение неполадок**.

    ![Сброс пароля](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Если эти рекомендации не помогли устранить проблему или они вам не подходят, см. статью [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). В ней описывается процесс диагностики и решения распространенных проблем.
