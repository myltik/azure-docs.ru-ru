---
title: Установка MySQL на виртуальную машину OpenSUSE | Microsoft Docs
description: Узнайте, как установить MySQL на виртуальную машину OpenSUSE Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn

---
# Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure
[MySQL][MySQL] — это популярная база данных SQL с открытым исходным кодом. В этом учебнике показано, как создать виртуальную машину под управлением OpenSUSE Linux, а затем установить MySQL.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

<br>

## Создание виртуальной машины под управлением OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Установка и запуск MySQL на виртуальной машине
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Дальнейшие действия
Дополнительные сведения о MySQL см. в [документации по MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0727_2016-->