---
title: Управление доступностью виртуальных машин Linux | Microsoft Docs
description: Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: cynthn

---
# Управление доступностью виртуальных машин
Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Linux в Azure. Вы также можете [управлять доступностью виртуальных машин Windows](virtual-machines-windows-manage-availability.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Инструкции по созданию группы доступности с использованием интерфейса командной строки в модели развертывания с помощью Resource Manager см. в разделе [Azure Availset. Команды для управления группами доступности](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## Дальнейшие действия
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](virtual-machines-linux-load-balance.md).

<!---HONumber=AcomDC_0601_2016-->