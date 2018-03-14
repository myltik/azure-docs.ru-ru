---
title: "включение файла"
description: "включение файла"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Поддерживаемые дистрибутивы и драйверы

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Драйверы NVIDIA CUDA для виртуальных машин серий NC, NCv2, NCv3 и ND
| Дистрибутив | Драйвер |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 или 7.4<br/><br/> CentOS 7.3 или 7.4 | NVIDIA CUDA 9.1, драйвер серии R390 |

> [!IMPORTANT]
> Убедитесь, что вы установили последнюю версию драйверов CUDA для своего дистрибутива или выполнили обновление до этой версии. В драйверах более ранних версий, чем R390, могут возникать проблемы при работе с обновленными ядрами Linux.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Драйверы NVIDIA GRID для виртуальных машин серии NV

| Дистрибутив | Драйвер |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Версия 7.3 на основе CentOS | NVIDIA GRID 5.2, ветвь драйверов R384|

> [!NOTE]
> Корпорация Майкрософт перераспределяет установщики драйверов NVIDIA GRID для виртуальных машин NV. Установите только эти драйверы GRID на виртуальных машинах Azure серии NV. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure.
>

> [!WARNING] 
> Установка стороннего программного обеспечения на продуктах Red Hat может нарушать условия технической поддержки Red Hat. Ознакомьтесь со [статьей из базы знаний Red Hat](https://access.redhat.com/articles/1067).
>
