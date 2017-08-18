---
title: "Управление доступностью виртуальных машин Windows в Azure | Документация Майкрософт"
description: "Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Windows в Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 408d030a7c26fea47669a690b0487e0868fbc2c9
ms.contentlocale: ru-ru
ms.lasthandoff: 08/11/2017

---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Управление доступностью виртуальных машин Windows в Azure 

Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Windows в Azure. Вы также можете [управлять доступностью виртуальных машин Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Инструкции по созданию и использованию групп доступности в классической модели развертывания см. в разделе [Как настроить группу доступности для виртуальных машин в классической модели развертывания](classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Дальнейшие действия
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


