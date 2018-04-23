---
title: REST API службы поиска Azure версии 2016-09-01-Preview | Документация Майкрософт
description: В версии 2016-09-01-Preview API REST службы поиска Azure реализованы экспериментальные функции, такие как поиски синонимов и запросы moreLikeThis.
author: mhko
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: bbfdb2641d68f1262cc6293462c7022701357374
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API REST службы поиска Azure, версия 2016-09-01-Preview
Эта статья представляет собой справочную документацию по версии `api-version=2016-09-01-Preview`. Она расширяет текущую общедоступную версию [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), изменения в которую заблокированы, и содержит перечисленные ниже экспериментальные функции.

* [API синонимов](search-synonyms.md) для отправки карт синонимов и расширенного поиска.
* [Параметр запроса `moreLikeThis`](search-more-like-this.md) для поиска документов, связанных с указанным документом.

Прежде чем использовать эти экспериментальные функции, убедитесь, что применяется предварительная версия API `api-version=2016-09-01-Preview`. В следующем примере показано, как указывается предварительная версия API при выполнении запроса more-like-this.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Функции предварительной версии доступны для тестирования и экспериментов с целью получения отзывов и могут изменяться. **Мы не рекомендуем использовать предварительные версии API в рабочих приложениях.**

Существуют различные версии службы поиска Azure. Дополнительные сведения см. в статье, посвященной [управлению версиями службы поиска](http://msdn.microsoft.com/library/azure/dn864560.aspx).
