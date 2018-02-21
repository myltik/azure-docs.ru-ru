---
title: "Событие начала удаления пула пакетной службы Azure | Документы Майкрософт"
description: "Справочник по событию начала удаления пула пакетной службы."
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
ms.openlocfilehash: f8a5241dce422e5c826ab428da6d7bc93284a1cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="pool-delete-start-event"></a>Событие начала удаления пула

 Это событие создается при запуске операции удаления пула. Так как удаление пула является асинхронным событием, можно ожидать, что после окончания этой операции возникнет событие завершения удаления пула.

 Ниже приведен пример текста для события начала удаления пула.

```
{
    "id": "myPool1"
}
```

|Элемент|Тип|Заметки|
|-------------|----------|-----------|
|id|Строка|Идентификатор пула.|