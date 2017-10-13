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
ms.openlocfilehash: 1e989c72fc03697bf6d2e515ff53003703665d1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Отмена и удаление заданий службы импорта и экспорта Azure

 Вы можете запросить отмену задания до того, как оно перейдет в состояние `Packaging`, вызвав операцию [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) и задав для элемента `CancelRequested` значение `true`. Задание будет отменено по мере возможности. Если диски используются для передачи данных, то передача данных может продолжаться даже после запроса отмены.

 Отмененное задание примет состояние `Completed` и будет храниться в течение 90 дней, после чего будет удалено.

 Чтобы удалить задание, вызовите операцию [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) перед доставкой задания (т. е. пока задание находится в состоянии `Creating`). Кроме того, задание можно удалить, когда оно находится в состоянии `Completed`. После удаления задания его данные и состояние станут недоступны через REST API или портал Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
