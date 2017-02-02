---
title: "Использование REST API службы импорта и экспорта Azure | Документация Майкрософт"
description: "Узнайте, как использовать REST API службы импорта и экспорта Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 675aef8ec0f9344d2b3073021e83856c2fb34b7c


---
# <a name="using-the-azure-importexport-service-rest-api"></a>Использование REST API службы импорта и экспорта Azure

Служба импорта и экспорта Microsoft Azure предоставляет REST API для программного управления заданиями импорта и экспорта. REST API можно использовать для всех операций импорта и экспорта, которые можно выполнять с помощью [портала Azure](https://portal.azure.com/). Кроме того, с помощью REST API можно выполнять определенные детализированные операции (например, запросить процент завершения задания), которые в настоящее время недоступны на классическом портале.

Общие сведения о службе импорта и экспорта, а также руководство по использованию классического портала Azure для создания заданий импорта и экспорта и управления ими см. в статье [Использование службы импорта и экспорта Azure для передачи данных в хранилище BLOB-объектов](storage-import-export-service.md).

## <a name="service-endpoints"></a>Конечные точки службы

Служба импорта и экспорта Azure представляет собой поставщик ресурсов для Azure Resource Manager. Она предоставляет набор интерфейсов REST API для управления заданиями импорта и экспорта в следующей конечной точке HTTPS:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Управление версиями

В запросах к службе импорта и экспорта необходимо указать параметр `api-version` со значением `2016-11-01`.

## <a name="in-this-section"></a>В этом разделе

[Создание задания импорта](storage-import-export-creating-an-import-job.md)

[Создание задания экспорта](storage-import-export-creating-an-export-job.md)

[Получение сведений о состоянии задания](storage-import-export-retrieving-state-info-for-a-job.md)

[Перечисление заданий](storage-import-export-enumerating-jobs.md)

[Отмена и удаление заданий](storage-import-export-cancelling-and-deleting-jobs.md)

[Резервное копирование манифестов диска](storage-import-export-backing-up-drive-manifests.md)

[Диагностика и восстановление после ошибок для заданий импорта и экспорта](storage-import-export-diagnostics-and-error-recovery.md)

## <a name="see-also"></a>См. также
 [Storage Import/Export REST](/rest/api/storageimportexport) (Справочник по API REST служб хранилища импорта и экспорта)



<!--HONumber=Dec16_HO3-->


