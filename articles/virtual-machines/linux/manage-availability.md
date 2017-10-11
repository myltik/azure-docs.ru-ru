---
title: "Управление доступностью виртуальных машин Linux в Azure | Документация Майкрософт"
description: "Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f153a740e4814e2573e53b9c051d24c30ff9088f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Управление доступностью виртуальных машин Linux

Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Linux в Azure. Вы также можете [управлять доступностью виртуальных машин Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Инструкции по созданию группы доступности с использованием интерфейса командной строки в модели развертывания с помощью Resource Manager см. в разделе [Azure Availset. Команды для управления группами доступности](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Дальнейшие действия
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](../virtual-machines-linux-load-balance.md).

