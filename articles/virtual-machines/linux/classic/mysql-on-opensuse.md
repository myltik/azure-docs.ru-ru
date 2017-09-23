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
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.contentlocale: ru-ru
ms.lasthandoff: 03/27/2017

---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure
[MySQL][MySQL] является популярной базой данных SQL с открытым кодом. В этом учебнике показано, как создать виртуальную машину под управлением OpenSUSE Linux, а затем установить MySQL.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Создание виртуальной машины под управлением OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Установка и запуск MySQL на виртуальной машине
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о MySQL см. в [этой документации][MySQLDocs].

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com


