---
title: Вычисление результатов теста производительности для виртуальных машин Windows в Azure | Документация Майкрософт
description: Сравнение результатов теста производительности SPECint для виртуальных машин Azure под управлением Windows Server.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 2831f177bbcd02be02124812de144bd7a6867aa4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424708"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Вычисление результатов теста производительности для виртуальных машин Windows
Ниже приведены результаты теста производительности SPECInt линейки высокопроизводительных виртуальных машин Azure под управлением Windows Server. Вы можете также ознакомиться с результатами теста производительности для [виртуальных машин Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE]
> Показатели для Linux были недавно обновлены и содержат более широкий набор виртуальных машин.

## <a name="a-series---compute-intensive"></a>Серия А: для ресурсоемких вычислений
| Размер | Число виртуальных ЦП | Число узлов NUMA | ЦП | Запуски | Ср. базовая производительность | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon E5-2670 с тактовой частотой 2,6 ГГц |10 |236,1 |1,1 |
| Standard_A9 |16 |2 |Intel Xeon E5-2670 с тактовой частотой 2,6 ГГц |10 |450,3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon E5-2670 с тактовой частотой 2,6 ГГц |5 |235,6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon E5-2670 с тактовой частотой 2,6 ГГц |7 |454,7 |4.8 |

## <a name="dv2-series"></a>Серия Dv2
| Размер | Число виртуальных ЦП | Число узлов NUMA | ЦП | Запуски | Ср. базовая производительность | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |83 |36,6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |27 |70,0 |3,7 |
| Standard_D3_v2 |4. |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |19 |130,5 |4.4. |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |19 |238,1 |5,2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |14 |460,9 |15,4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |19 |70,1 |3,7 |
| Standard_D12_v2 |4. |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |2 |132,0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |17 |235,8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 вер. 3 с тактовой частотой 2,4 ГГц |15 |460,8 |6,5 |

## <a name="g-series-gs-series"></a>Серии G и GS
| Размер | Число виртуальных ЦП | Число узлов NUMA | ЦП | Запуски | Ср. базовая производительность | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B вер. 3 с тактовой частотой 2 ГГц |31 |71,8 |6,5 |
| Standard_G2, Standard_GS2 |4. |1 |Intel Xeon E5-2698B вер. 3 с тактовой частотой 2 ГГц |5 |133,4 |13,0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B вер. 3 с тактовой частотой 2 ГГц |6 |242,3 |6,0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B вер. 3 с тактовой частотой 2 ГГц |15 |398,9 |6,0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B вер. 3 с тактовой частотой 2 ГГц |22 |762,8 |3,7 |

## <a name="h-series"></a>Серия H
| Размер | Число виртуальных ЦП | Число узлов NUMA | ЦП | Запуски | Ср. базовая производительность  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 вер. 3 с тактовой частотой 3,2 ГГц |5 |297,4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 вер. 3 с тактовой частотой 3,2 ГГц |5 |575,8 |6,8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 вер. 3 с тактовой частотой 3,2 ГГц |5 |297,0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 вер. 3 с тактовой частотой 3,2 ГГц |5 |572,2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 вер. 3 с тактовой частотой 3,2 ГГц |5 |573,2 |2,9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 вер. 3 с тактовой частотой 3,2 ГГц |7 |569,6 |2.8 |

## <a name="about-specint"></a>О SPECint
Полученные результаты для Windows были вычислены посредством запуска [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) в Windows Server. Для запуска SPECint был использован режим базовой производительности (SPECint_rate2006) с одной копией на виртуальный ЦП. SPECint содержит 12 отдельных тестов, каждый из которых выполняется трижды, после чего срединное значение каждого теста взвешивается для получения составного показателя. Эти тесты были выполнены на нескольких виртуальных машинах для получения приведенных средних показателей.

## <a name="next-steps"></a>Дополнительная информация
* Сведения о емкости хранилища, подробности о дисках и дополнительные рекомендации по выбору размера виртуальной машины см. в статье [Размеры виртуальных машин в Azure](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

