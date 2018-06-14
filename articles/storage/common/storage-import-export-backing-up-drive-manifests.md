---
title: Архивация манифестов дисков для импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как настроить автоматическую архивацию манифестов диска для службы импорта и экспорта Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059329"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Архивация манифестов дисков для заданий импорта и экспорта Azure

Манифесты диска можно архивировать автоматически в большие двоичные объекты, задав значение `true` для свойства `BackupDriveManifest` при операции REST API [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) или [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). По умолчанию манифесты диска не архивируются. Резервные копии манифестов диска хранятся в виде блочных BLOB-объектов в контейнере, расположенном в учетной записи хранения, которая связана с заданием. По умолчанию контейнер называется `waimportexport`, но при вызове операции `Put Job` или `Update Job Properties` в свойстве `DiagnosticsPath` можно указать другое имя. Имя большого двоичного объекта с резервными копиями манифестов имеет следующий формат: `waies/jobname_driveid_timestamp_manifest.xml`.

 Универсальный код ресурса (URI) резервных копий манифестов диска для задания можно получить, вызвав операцию [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). Универсальный код ресурса (URI) большого двоичного объекта возвращается в свойство `ManifestUri` для каждого диска.

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
