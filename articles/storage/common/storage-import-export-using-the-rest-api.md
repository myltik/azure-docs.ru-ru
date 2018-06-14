---
title: Использование REST API службы импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, где найти документацию по использованию REST API службы импорта и экспорта Azure, включая практические руководства и справочные материалы.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 9a5a97a5d9f06aa73f1ad521e112fa25f215724f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
ms.locfileid: "26741066"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Использование REST API службы импорта и экспорта Azure

Служба импорта и экспорта Microsoft Azure предоставляет REST API для программного управления заданиями импорта и экспорта. REST API можно использовать для всех операций импорта и экспорта, которые можно выполнять с помощью [портала Azure](https://portal.azure.com/). Кроме того, с помощью REST API можно выполнять определенные детализированные операции (например, запросить процент завершения задания), которые в настоящее время недоступны на портале Azure.

Общие сведения о службе импорта и экспорта, а также руководство по использованию портала Azure создания заданий импорта и экспорта и управления ими см. в статье об [использовании службы импорта и экспорта Azure для передачи данных в хранилище BLOB-объектов](../storage-import-export-service.md).

## <a name="service-endpoints"></a>Конечные точки службы

Служба импорта и экспорта Azure представляет собой поставщик ресурсов для Azure Resource Manager. Она предоставляет набор интерфейсов REST API для управления заданиями импорта и экспорта в следующей конечной точке HTTPS:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Управление версиями

В запросах к службе импорта и экспорта необходимо указать параметр `api-version` со значением `2016-11-01`.

## <a name="importexport-service-operations"></a>Операции службы импорта и экспорта

[Создание задания импорта](../storage-import-export-creating-an-import-job.md)

[Создание задания экспорта](../storage-import-export-creating-an-export-job.md)

[Получение сведений о состоянии задания](storage-import-export-retrieving-state-info-for-a-job.md)

[Перечисление заданий](../storage-import-export-enumerating-jobs.md)

[Отмена и удаление заданий](storage-import-export-cancelling-and-deleting-jobs.md)

[Архивация манифестов дисков](../storage-import-export-backing-up-drive-manifests.md)

[Диагностика и восстановление после ошибок для заданий импорта и экспорта](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Дополнительная информация

* [Storage Import/Export REST](/rest/api/storageimportexport) (Справочник по API REST служб хранилища импорта и экспорта)
