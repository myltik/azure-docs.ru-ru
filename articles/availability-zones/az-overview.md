---
title: "Общие сведения о зонах доступности | Документация Майкрософт"
description: "В этой статье представлены общие сведения о зонах доступности в Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: db4801d105a6ca7382756224aa84c9e7b305194f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Общие сведения о зонах доступности в Azure (предварительная версия)

Зоны доступности помогают защититься от сбоев на уровне центра обработки данных. Зоны расположены в пределах региона Azure. У каждой их них есть собственный независимый источник питания, сеть и система охлаждения. Чтобы обеспечить устойчивость, во всех включенных областях используются минимум три отдельные зоны. Физическое и логическое разделение зон доступности в пределах региона защищает приложения и данные от сбоев на уровне зоны. 

![Сбой одной зоны в пределах региона](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Регионы с поддержкой зон доступности

- Восток США 2
- Центральная часть США
- Западная Европа
- Центральная Франция

## <a name="services-that-support-availability-zones"></a>Службы с поддержкой зон доступности

Службы Azure с поддержкой зон доступности:

- Виртуальные машины Linux
- Виртуальные машины Windows
- Наборы для масштабирования виртуальных машин
- Управляемые диски
- Подсистема балансировки нагрузки
- Общедоступный IP-адрес
- База данных SQL

## <a name="get-started-with-the-availability-zones-preview"></a>Начало работы с предварительной версией зон доступности

Предварительная версия зон доступности предлагается для определенных служб Azure в таких регионах: восточная часть США 2, центральная часть США, западная Европа и центральная Франция. 

1. [Зарегистрируйтесь для использования предварительной версии зон доступности](http://aka.ms/azenroll). 
2. Войдите в свою подписку Azure.
3. Выберите регион с поддержкой зон доступности.
4. Перейдите по одной из следующих ссылок, чтобы начать использовать зоны доступности со своей службой. 
    - [Создание виртуальной машины](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Создание масштабируемого набора виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [Добавление управляемого диска с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Подсистема балансировки нагрузки](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Дополнительная информация
- [Шаблоны быстрого запуска](http://aka.ms/azqs)
