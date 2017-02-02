---
title: "Перечисление заданий в службе импорта и экспорта Azure | Документация Майкрософт"
description: "Узнайте, как перечислить все задания службы импорта и экспорта Azure в подписке."
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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>Перечисление заданий
Чтобы перечислить все задания в подписке, вызовите операцию [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` возвращает список заданий, а также перечисленные ниже атрибуты:

-   тип задания (импорт или экспорт);

-   текущее состояние задания;

-   связанная с задание учетная запись хранения.

## <a name="see-also"></a>См. также
 [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


