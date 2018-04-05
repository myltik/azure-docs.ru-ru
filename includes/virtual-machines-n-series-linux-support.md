---
title: включение файла
description: включение файла
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5dc0636fa4bfede460b1c8cd8bdab5628a110578
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
## <a name="supported-distributions-and-drivers"></a>Поддерживаемые дистрибутивы и драйверы

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Драйверы NVIDIA CUDA для виртуальных машин серий NC, NCv2, NCv3 и ND

Сведения о драйвере CUDA в таблице ниже актуальны на время публикации. Последние версии драйверов CUDA можно получить на веб-сайте [NVIDIA](https://developer.nvidia.com/cuda-zone). Убедитесь, что вы установили последнюю версию драйверов CUDA для своего дистрибутива или выполнили обновление до этой версии. 

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Linux можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Ubuntu 16.04 LTS или CentOS 7.4 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.

| Дистрибутив | Драйвер |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 или 7.4<br/><br/> CentOS 7.3 или 7.4, 7.4 HPC на основе CentOS | NVIDIA CUDA 9.1, драйвер серии R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>Драйверы NVIDIA GRID для виртуальных машин серии NV

Корпорация Майкрософт перераспределяет установщики драйверов NVIDIA GRID для виртуальных машин NV. Установите только эти драйверы GRID на виртуальных машинах Azure серии NV. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure.

| Дистрибутив | Драйвер |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Версия 7.3 на основе CentOS | NVIDIA GRID 5.2, ветвь драйверов R384|



> [!WARNING] 
> Установка стороннего программного обеспечения на продуктах Red Hat может нарушать условия технической поддержки Red Hat. Ознакомьтесь со [статьей из базы знаний Red Hat](https://access.redhat.com/articles/1067).
>
