
---
title: "Размеры виртуальных машин Windows в Azure | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Windows в Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 365d6ad9ec0e0a7ad8d9742d863540646257e298
ms.lasthandoff: 03/27/2017

---

# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Размеры виртуальных машин Windows в Azure

В этой статье описаны доступные размеры и разновидности виртуальных машин Azure, которые можно использовать для запуска приложений для Windows и рабочих нагрузок Windows. Здесь также предоставлены рекомендации по развертыванию, которые нужно учитывать при планировании использования этих ресурсов.  Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!IMPORTANT]
>* Подробнее о ценах на различные размеры см. в разделе [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
>* Сведения об общих ограничениях виртуальных машин Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
>* Стоимость хранилища рассчитывается отдельно в зависимости от количества страниц, используемых в учетной записи хранения. Дополнительные сведения см. на странице [Цены на хранилища Azure](https://azure.microsoft.com/pricing/details/storage/).
> * Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](virtual-machines-windows-acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
>
>
<br>    




| Тип                     | Размеры           |    Описание       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Универсальные](virtual-machines-windows-sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0–A7 | Сбалансированное соотношение ресурсов ЦП и памяти. Идеальное решение для тестирования и разработки, небольших и средних баз данных, а также веб-серверов с небольшим или средним объемом трафика. |
| [Оптимизированные для вычислений](virtual-machines-windows-sizes-compute.md)        | Fs, F             | Высокое соотношение ресурсов ЦП и памяти. Подходят для веб-серверов со средним объемом трафика, сетевых устройств, пакетных процессов и серверов приложений.        |
| [Оптимизированные для памяти](virtual-machines-windows-sizes-memory.md)         | GS, G, DSv2, DS   | Высокое соотношение ресурсов памяти и ядер. Отлично подходят для серверов реляционной базы данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| [Оптимизированные для хранилища](virtual-machines-windows-sizes-storage.md)        | Ls                | Высокая пропускная способность дисков и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, а также с базами данных SQL и NoSQL.                                                         |
| [GPU](virtual-machines-windows-sizes-gpu.md)            | NV, NC            | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео. Доступны виртуальные машины одним или несколькими GPU.       |
| [Для высокопроизводительных вычислений](virtual-machines-windows-sizes-hpc.md) | H, A8–A11          | Виртуальные машины с самыми быстрыми и мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). 

<br>

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](virtual-machines-windows-acu.md) сравнить производительность вычислений для различных номеров SKU Azure.

Узнайте больше о различных доступных размерах виртуальных машин.
- [Универсальные](virtual-machines-windows-sizes-general.md)
- [Оптимизированные для вычислений](virtual-machines-windows-sizes-compute.md)
- [Оптимизированные для памяти](virtual-machines-windows-sizes-memory.md)
- [Оптимизированные для хранилища](virtual-machines-windows-sizes-storage.md)
- [Оптимизированные для GPU](virtual-machines-windows-sizes-gpu.md)
- [Для высокопроизводительных вычислений](virtual-machines-windows-sizes-hpc.md)




