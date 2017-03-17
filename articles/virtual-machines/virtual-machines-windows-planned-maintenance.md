---
title: "Плановое обслуживание виртуальных машин Windows | Документация Майкрософт"
description: "Сведения о том, что такое плановое обслуживание Azure и как оно влияет на виртуальные машины Windows, работающие в Azure."
services: virtual-machines-windows
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: fecc7666f80f7a9a5b2f8daaa65fd1766616fc85
ms.lasthandoff: 03/03/2017


---
# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Плановое обслуживание виртуальных машин в Azure
Сведения о том, что такое плановое обслуживание Azure и как оно влияет на доступность виртуальных машин Windows. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье содержатся общие сведения о процессе планового технического обслуживания Azure. Если вы хотите определить причину перезагрузки виртуальной машины, см. [эту запись блога, посвященную просмотру журналов перезагрузки виртуальной машины](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Причины выполнения Azure планового обслуживания
Microsoft Azure периодически выполняет обновления своих систем по всему миру, чтобы повысить надежность, производительность и безопасность инфраструктуры узлов, в которой работают виртуальные машины. Многие из таких обновлений, включая обновления с сохранением памяти, выполняются без какого-либо влияния на виртуальные машины и облачные службы.

Тем не менее, при некоторых обновлениях требуется перезагрузка виртуальных машин для применения обязательных обновлений в инфраструктуре. При применении исправлений в инфраструктуре необходимо завершить работу виртуальных машин, а затем снова запустить их.

Обратите внимание, что на доступность ваших виртуальных машин могут повлиять два типа обслуживания: плановое и внеплановое. На этой странице описывается, как выполняется плановое обслуживание Microsoft Azure. Дополнительные сведения о внеплановом обслуживании см. в разделе [Различия планового и внепланового обслуживания](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

