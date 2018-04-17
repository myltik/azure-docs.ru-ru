---
title: включение файла
description: включение файла
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn;davberg
ms.custom: include file
ms.openlocfilehash: fde43e40a7a5bb87b9e63af47ae795616fac8b3f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
Концепция единицы вычислений Azure (ACU) позволяет сравнивать производительность ЦП разных номеров SKU Azure. Это поможет вам легко определить, какие SKU с наибольшей вероятностью удовлетворят ваши требования к производительности.  На данный момент в качестве стандарта единицы ACU выбрана малая ВМ (Standard_A1), равная 100, и все прочие SKU представляют то, насколько быстрее данный SKU может выполнять стандартную нагрузку. 

> [!IMPORTANT]
> Единица ACU используется только для справки.  Фактические результаты для конкретной рабочей нагрузки могут отличаться. 
> 
> 

<br>

| Семейство SKU | ACU \ виртуальные ЦП | Виртуальный процессор: ядро |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1–A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5–A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2–A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2–A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1–D14](../articles/virtual-machines/windows/sizes-general.md) |160 | 1:1 |
| [D1_v2–D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210–250* | 1:1 |
| [DS1–DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 | 1:1 |
| [DS1_v2–DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210–250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\* |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\* |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\* |
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\* |
| [F2s_v2-F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195-210* | 2:1\*\* |
| [F1–F16](../articles/virtual-machines/windows/sizes-compute.md) |210–250* | 1:1 |
| [F1s–F16s](../articles/virtual-machines/windows/sizes-compute.md) |210–250* | 1:1 |
| [G1–G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180–240* | 1:1 |
| [GS1–GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180–240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290–300* | 1:1 |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180–240* | 1:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160-180 | 2:1\*\* |

Единицы ACU, помеченные * , основаны на технологии Intel® Turbo для увеличения частоты ЦП и значительного повышения производительности.  Степень увеличения производительности может различаться в зависимости от размера виртуальной машины, рабочей нагрузки и других рабочих нагрузок, выполняющихся на том же узле.

** С поддержкой технологии Hyper-Threading. 
