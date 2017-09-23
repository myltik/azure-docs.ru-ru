---
title: "Событие завершения удаления пула пакетной службы Azure | Документы Майкрософт"
description: "Справочник по событию завершения удаления пула пакетной службы."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 890f2ba7fda37060c56177868d6214d517d91831
ms.contentlocale: ru-ru
ms.lasthandoff: 04/22/2017

---

# <a name="pool-delete-complete-event"></a>Событие завершения удаления пула

 Это событие создается при завершении операции удаления пула.

 Ниже приведен пример текста для события завершения удаления пула.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Элемент|Тип|Примечания|
|-------------|----------|-----------|
|id|string|Идентификатор пула.|
|startTime|DateTime|Время, когда было начато удаление пула.|
|endTime|DateTime|Время, когда удаление пула было завершено.|

## <a name="remarks"></a>Примечания
Дополнительные сведения о состояниях и кодах ошибок для операции изменения размера пула см. в статье [Удаление пула из учетной записи](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
