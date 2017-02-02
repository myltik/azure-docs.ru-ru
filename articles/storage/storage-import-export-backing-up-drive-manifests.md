---
title: "Архивация манифестов дисков для импорта и экспорта Azure | Документация Майкрософт"
description: "Узнайте, как настроить автоматическую архивацию манифестов диска для службы импорта и экспорта Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 3d3abfb93ba09aa9f09258743ef6d4c1fc932ae5


---

# <a name="backing-up-drive-manifests"></a>Архивация манифестов диска
Манифесты диска можно архивировать автоматически в большие двоичные объекты, задав значение `true` для свойства `BackupDriveManifest` при операции [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) или [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). По умолчанию манифесты диска не архивируются. Резервные копии манифестов диска хранятся в виде блочных BLOB-объектов в контейнере, расположенном в учетной записи хранения, которая связана с заданием. По умолчанию контейнер называется `waimportexport`, но при вызове операции `Put Job` или `Update Job Properties` в свойстве `DiagnosticsPath` можно указать другое имя. Имя большого двоичного объекта с резервными копиями манифестов имеет следующий формат: `waies/jobname_driveid_timestamp_manifest.xml`.

 Универсальный код ресурса (URI) резервных копий манифестов диска для задания можно получить, вызвав операцию [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). Универсальный код ресурса (URI) большого двоичного объекта возвращается в свойство `ManifestUri` для каждого диска.

## <a name="see-also"></a>См. также
 [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


