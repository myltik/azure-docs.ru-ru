---
title: "Отмена и удаление задания импорта и экспорта Azure | Документация Майкрософт"
description: "Узнайте, как отменять и удалять задания службы импорта и экспорта Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 74182c8c357085f186aaa43adfaef80a083d16bb
ms.openlocfilehash: 26e06529cb9354030da32c235d3c4819d46d669d
ms.lasthandoff: 02/16/2017


---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Отмена и удаление заданий службы импорта и экспорта Azure
Вы можете запросить отмену задания до того, как оно перейдет в состояние `Packaging`, вызвав операцию состояния путем вызова [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) и задав для элемента `CancelRequested` значение `true`. Задание будет отменено наилучшим образом. Если диски используются для передачи данных, то передача данных может продолжаться даже после запроса отмены.

 Отмененное задание перейдет в состояние `Completed` и будет храниться 90 дней, после чего оно будет удалено.

 Чтобы удалить задание, вызовите операцию [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) перед доставкой задания (*т. е.* пока задание находится в состоянии `Creating`). Кроме того, задание можно удалить, когда оно находится в состоянии `Completed`. После удаления задания его данные и состояние станут недоступны через REST API или портал Azure.

## <a name="see-also"></a>См. также
 [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)

