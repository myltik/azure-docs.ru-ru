---
title: "Размеры виртуальных машин Linux в Azure, оптимизированных для GPU | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Linux в Azure, оптимизированных для GPU. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии."
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
ms.date: 12/14/2017
ms.author: jonbeck
ms.openlocfilehash: 9e8b682d8d7e409f97144d344ec58e710327df7a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Инструкции по установке и проверке драйверов см. в статье [Установка драйверов GPU NVIDIA на виртуальные машины серии N под управлением Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Не следует устанавливать X-сервер или другие системы, использующие драйвер `Nouveau`, на виртуальные машины NC под управлением Ubuntu. Перед установкой драйверов NVIDIA для GPU необходимо отключить драйвер `Nouveau`.  

## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.