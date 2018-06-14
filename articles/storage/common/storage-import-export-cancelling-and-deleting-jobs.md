---
title: Отмена и удаление задания импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как отменять и удалять задания службы импорта и экспорта Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3524f1677baaa218b009b8498b851390c7b9da9a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367020"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Отмена и удаление заданий службы импорта и экспорта Azure

 Вы можете запросить отмену задания до того, как оно перейдет в состояние `Packaging`, вызвав операцию [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) и задав для элемента `CancelRequested` значение `true`. Задание будет отменено по мере возможности. Если диски используются для передачи данных, то передача данных может продолжаться даже после запроса отмены.

 Отмененное задание примет состояние `Completed` и будет храниться в течение 90 дней, после чего будет удалено.

 Чтобы удалить задание, вызовите операцию [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) перед доставкой задания (т. е. пока задание находится в состоянии `Creating`). Кроме того, задание можно удалить, когда оно находится в состоянии `Completed`. После удаления задания его данные и состояние станут недоступны через REST API или портал Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
