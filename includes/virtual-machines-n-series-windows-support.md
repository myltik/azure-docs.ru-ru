---
title: включение файла
description: включение файла
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 689e45f96ed5e7aaea3aecd1193e9cd58f10e80d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>Драйверы NVIDIA Tesla (CUDA) для виртуальных машин серий NC, NCv2, NCv3 и ND

Ссылки для скачивания драйверов, приведенные в таблице, актуальны на момент ее публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Windows Server можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Windows Server 2016 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.


| ОС | Драйвер |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (EXE-файл) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (EXE-файл) |

### <a name="nv-series---nvidia-grid-drivers"></a>Драйверы NVIDIA GRID для виртуальных машин серии NV

Корпорация Майкрософт перераспределяет установщики драйверов NVIDIA GRID для виртуальных машин NV. Установите только эти драйверы GRID на виртуальных машинах Azure серии NV. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure.

| ОС | Драйвер |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (EXE-файл) |
| Windows Server 2012 R2 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (EXE-файл)  |