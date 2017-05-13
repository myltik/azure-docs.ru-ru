---
title: "Размеры виртуальных машин Linux в Azure, оптимизированных для GPU | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Linux в Azure, оптимизированных для GPU."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: e0cd2a14b4102797024925a72783ecaf56919aec
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---

# <a name="gpu-linux-vm-sizes"></a>Размеры виртуальных машин Linux, оптимизированных для GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Сведения о поддерживаемых операционных системах и требованиях к драйверам доступны в разделе [Установка драйверов GPU для виртуальных машин серии N под управлением Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

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
