---
title: "включение файла"
description: "включение файла"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>Драйверы NVIDIA Tesla для виртуальных машин серий NC, NCv2, NCv3 и ND

| ОС | Драйвер |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (EXE-файл) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (EXE-файл) |

> [!NOTE]
> Ссылки для скачивания драйверов Tesla актуальны на момент публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).
>

### <a name="nv-series---nvidia-grid-drivers"></a>Драйверы NVIDIA GRID для виртуальных машин серии NV

| ОС | Драйвер |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (EXE-файл) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (EXE-файл)  |

> [!NOTE]
> Корпорация Майкрософт перераспределяет установщики драйверов NVIDIA GRID для виртуальных машин NV. Установите только эти драйверы GRID на виртуальных машинах Azure серии NV. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure.
>