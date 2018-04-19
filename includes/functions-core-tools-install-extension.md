---
title: включение файла
description: включение файла
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
Если вы разрабатываете функции локально, можете установить необходимые расширения из терминала или командной строки с помощью основных инструментов службы "Функции Azure". В следующем примере команда `func extensions install` позволяет установить расширение привязки Azure Cosmos DB:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Замените `<taget_version>` определенной версией пакета. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org).
