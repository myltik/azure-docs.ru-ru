---
title: "Плановое обслуживание виртуальных машин Linux | Документация Майкрософт"
description: "Сведения о том, что такое плановое обслуживание Azure и как оно влияет на виртуальные машины Linux, работающие в Azure."
services: virtual-machines-linux
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e65e614c-e969-40dc-a271-fe074069daf1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d0e7d00dd8e6ab53897340e13a3170519cbdb135


---
# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Плановое обслуживание виртуальных машин Linux в Azure
Сведения о том, что такое плановое обслуживание Azure и как оно влияет на доступность виртуальных машин Linux. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

В этой статье содержатся общие сведения о процессе планового технического обслуживания Azure. Если вы хотите определить причину перезагрузки виртуальной машины, см. [эту запись блога, посвященную просмотру журналов перезагрузки виртуальной машины](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Причины выполнения Azure планового обслуживания
Microsoft Azure периодически выполняет обновления своих систем по всему миру, чтобы повысить надежность, производительность и безопасность инфраструктуры узлов, в которой работают виртуальные машины. Многие из таких обновлений, включая обновления с сохранением памяти, выполняются без какого-либо влияния на виртуальные машины и облачные службы.

Тем не менее, при некоторых обновлениях требуется перезагрузка виртуальных машин для применения обязательных обновлений в инфраструктуре. При применении исправлений в инфраструктуре необходимо завершить работу виртуальных машин, а затем снова запустить их.

Обратите внимание, что на доступность ваших виртуальных машин могут повлиять два типа обслуживания: плановое и внеплановое. На этой странице описывается, как выполняется плановое обслуживание Microsoft Azure. Дополнительные сведения о внеплановом обслуживании см. в разделе [Различия планового и внепланового обслуживания](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]




<!--HONumber=Nov16_HO3-->


