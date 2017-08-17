---
title: "Поддержка добавления виртуальных машин Azure в существующую группу доступности | Документы Майкрософт"
description: "Поддержка добавления виртуальных машин Azure в существующую группу доступности."
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: f07108f5d7a792dcc9aae8a4fe889878db168bc6
ms.contentlocale: ru-ru
ms.lasthandoff: 07/21/2017

---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Поддержка добавления виртуальных машин Azure в существующую группу доступности

Иногда вы можете столкнуться с ограничениями при добавлении новых виртуальных машин в существующую группу доступности. На следующей диаграмме показано, какие серии виртуальных машин можно объединить в одной группе доступности.

Ниже приводится матрица поддержки для объединения различных типов виртуальных машин:

Серии и группа доступности|Вторая виртуальная машина|Файл ,|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Первая виртуальная машина|||||||
|Файл ,||ОК|ОК|ОК|ОК|ОК|
|Av2||ОК|ОК|ОК|ОК|ОК|
|D||ОК|ОК|ОК|ОК|ОК|
|Dv2||ОК|ОК|ОК|ОК|ОК|
|Dv3||ОК|ОК|ОК|ОК|ОК|

Все остальные серии не могут находиться в одной и той же группе доступности, так как для них требуется определенное оборудование.
