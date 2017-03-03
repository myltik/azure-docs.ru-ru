---
title: "Вывод списка всех заданий импорта и экспорта Azure | Документация Майкрософт"
description: "Узнайте, как вывести список всех заданий службы импорта и экспорта Azure в подписке."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 50fbd0d214c825137c3ac6873be27b9d2d53cee1
ms.openlocfilehash: 5bbe99110b86cb15d23eadc48c1ea45bbab13daf
ms.lasthandoff: 02/16/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Перечисление заданий в службе импорта и экспорта Azure
Чтобы перечислить все задания в подписке, вызовите операцию [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` возвращает список заданий, а также перечисленные ниже атрибуты:

-   тип задания (импорт или экспорт);

-   текущее состояние задания;

-   связанная с задание учетная запись хранения.

## <a name="see-also"></a>См. также
 [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)

