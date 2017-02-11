---
title: "Установка MySQL на виртуальную машину OpenSUSE | Документация Майкрософт"
description: "Узнайте, как установить MySQL на виртуальную машину OpenSUSE Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ff9e23e9324cc47ae1a5d7cb52f13920000b8557


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure
[MySQL][MySQL] является популярной базой данных SQL с открытым кодом. В этом учебнике показано, как создать виртуальную машину под управлением OpenSUSE Linux, а затем установить MySQL.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Создание виртуальной машины под управлением OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Установка и запуск MySQL на виртуальной машине
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о MySQL см. в [этой документации][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Feb17_HO2-->


