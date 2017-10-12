---
title: "Размеры виртуальных машин Linux в Azure, оптимизированных для GPU | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Linux в Azure, оптимизированных для GPU."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: 5c9bf89feba519147b07f2810fe4da882664e89e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="gpu-linux-vm-sizes"></a>Размеры виртуальных машин Linux, оптимизированных для GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Инструкции по установке и проверке драйверов см. в статье [Установка драйверов GPU NVIDIA на виртуальные машины серии N под управлением Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Мы не рекомендуем устанавливать X server или другие системы, использующие драйвер nouveau, на виртуальные машины NC под управлением Ubuntu. Перед установкой драйверов NVIDIA GPU необходимо отключить драйвер nouveau.  

## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.