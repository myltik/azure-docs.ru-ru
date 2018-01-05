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
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: b3618207c1d4d2b8d3c0eaf83408b4813cd3da2d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
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

Предварительный просмотр зон доступности доступен в восточная часть США 2, Западной Европе и Франции центральном регионах для определенных служб Azure. 

1. [Зарегистрируйтесь для использования предварительной версии зон доступности](http://aka.ms/azenroll). 
2. Войдите в свою подписку Azure.
3. Выберите регион с поддержкой зон доступности.
4. Перейдите по одной из следующих ссылок, чтобы начать использовать зоны доступности со своей службой. 
    - [Создание виртуальной машины](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Создание набора масштабирования виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [Добавление управляемого диска с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Подсистема балансировки нагрузки](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Дальнейшие действия
- [Шаблоны быстрого запуска](http://aka.ms/azqs)
