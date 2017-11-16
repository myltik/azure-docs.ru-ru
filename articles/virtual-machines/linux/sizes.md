---
title: "Размеры виртуальных машин Linux в Azure | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 7c6f2e83e5763f3cf8f305c73c0ad74fa8b37c00
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Размеры виртуальных машин Linux в Azure
В этой статье описаны доступные размеры и разновидности виртуальных машин Azure, которые можно использовать для запуска приложений и рабочих нагрузок Linux. Здесь также предоставлены рекомендации по развертыванию, которые нужно учитывать при планировании использования этих ресурсов. Также доступна версия этой статьи для [виртуальных машин Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Тип                     | Размеры           |    Описание       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Универсальные](sizes-general.md)          | B (предварительная версия), Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7  | Сбалансированное соотношение ресурсов ЦП и памяти. Идеальное решение для тестирования и разработки, небольших и средних баз данных, а также веб-серверов с небольшим или средним объемом трафика. |
| [Оптимизированные для вычислений](sizes-compute.md)        | Fsv2 Fs, F             | Высокое соотношение ресурсов ЦП и памяти. Подходят для веб-серверов со средним объемом трафика, сетевых устройств, пакетных процессов и серверов приложений.        |
| [Оптимизированные для памяти](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Высокое соотношение ресурсов памяти и ядра. Отлично подходят для серверов реляционной базы данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| [Оптимизированные для хранилища](sizes-storage.md)        | Ls                | Высокая пропускная способность дисков и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, а также с базами данных SQL и NoSQL.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC            | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео. Доступны виртуальные машины одним или несколькими GPU.       |
| [Для высокопроизводительных вычислений](sizes-hpc.md) | H, A8–A11          | Виртуальные машины с самыми быстрыми и мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). 

<br>

- Подробнее о ценах на различные размеры см. в разделе [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Доступность размеров виртуальных машин см. в статье [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).
- Сведения об общих ограничениях виртуальных машин Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md).
- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](../windows/acu.md) сравнить производительность вычислений для различных номеров SKU Azure.


## <a name="rest-api"></a>Rest API

Сведения об использовании Rest API, чтобы получить сведения о размерах виртуальных машин, см. в следующих статьях:

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing) (Вывод доступных размеров виртуальных машин для изменения размеров)
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region) (Вывод доступных размеров виртуальных машин для подписки)
- [List available virtual machine sizes in an availability set](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set) (Вывод доступных размеров виртуальных машин в группе доступности)

## <a name="acu"></a>ACU

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о различных доступных размерах виртуальных машин.
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)



