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
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: c3a1758ab965d22eabfe44a410e9f72420e0148b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Общие сведения о зонах доступности в Azure (предварительная версия)

Зоны доступности помогают защититься от сбоев на уровне центра обработки данных. Зоны расположены в пределах региона Azure. У каждой их них есть собственный независимый источник питания, сеть и система охлаждения. Чтобы обеспечить устойчивость, во всех включенных областях используются минимум три отдельные зоны. Физическое и логическое разделение зон доступности в пределах региона защищает приложения и данные от сбоев на уровне зоны. 

![Сбой одной зоны в пределах региона](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Регионы с поддержкой зон доступности

- Восток США 2
- Западная Европа
- Центральная Франция

## <a name="services-that-support-availability-zones"></a>Службы с поддержкой зон доступности

Службы Azure с поддержкой зон доступности:

- Виртуальные машины Linux
- Виртуальные машины Windows
- Связанные с зонами масштабируемые наборы виртуальных машин
- Управляемые диски
- Подсистема балансировки нагрузки
- Общедоступный IP-адрес

## <a name="get-started-with-the-availability-zones-preview"></a>Начало работы с предварительной версией зон доступности

Предварительная версия зон доступности предлагается для определенных служб Azure в таких регионах: восточная часть США 2, западная Европа и центральная Франция. 

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
