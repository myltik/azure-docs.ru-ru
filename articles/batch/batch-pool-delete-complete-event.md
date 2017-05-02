---
title: "Событие завершения удаления пула | Документы Майкрософт"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 580a1278-5740-4143-826c-7d875ef127ff
caps.latest.revision: 5
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 991ee552ccc564be149844b58a0a3fea7452f2a3
ms.lasthandoff: 04/13/2017

---
# <a name="pool-delete-complete-event"></a>Событие завершения удаления пула
Основная часть журнала событий по завершению удаления пула

## <a name="remarks"></a>Примечания
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
|id|Строка|Идентификатор пула.|
|startTime|DateTime|Время, когда было начато удаление пула.|
|endTime|DateTime|Время, когда удаление пула было завершено.|

## <a name="remarks"></a>Примечания
Дополнительные сведения о состояниях и кодах ошибок для операции изменения размера пула см. в статье [Удаление пула из учетной записи](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
