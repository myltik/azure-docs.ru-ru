---
title: "Как просматривать связанные ресурсы данных в каталоге данных Azure | Документация Майкрософт"
description: "В этой статье объясняется, как просматривать связанные ресурсы данных в каталоге данных Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: da7ac8f323f9b2ec6303fbc85aa570373a538bcf
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Как просматривать связанные ресурсы данных в каталоге данных Azure
Каталог данных Azure позволяет просматривать ресурсы данных, связанные с выбранным ресурсом, и связи между ними. 

## <a name="supported-data-sources"></a>Поддерживаемые источники данных 
При регистрации ресурсов данных из следующих источников данных каталог данных Azure автоматически регистрирует метаданные о связях соединения между выбранными ресурсами данных. 

- SQL Server
- База данных SQL Azure
- MySQL
- Oracle

> [!NOTE]
> Чтобы в каталоге данных импортировать связь между двумя ресурсами данных, необходимо одновременно зарегистрировать оба ресурса. Если один из них добавлен отдельно, добавьте его и другой ресурс еще раз, чтобы импортировать связи между этими ресурсами.

## <a name="view-related-data-assets"></a>Просмотр связанных ресурсов данных
Для просмотра ресурсов данных, связанных с выбранным набором данных, используйте вкладку **Связи**, как показано на следующем рисунке: 

![Просмотр связанных ресурсов данных в каталоге данных Azure](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

В этом примере у выбранного ресурса данных **ProductSubcategory** имеется две связи: 

- Столбец ProductSubcategoryID таблицы Product имеет связь по внешнему ключу со столбцом ProductSubcategoryID выбранной таблицы ProductSubcategory. 
- Столбец ProductCategoryID таблицы ProductSubCategory имеет связь по внешнему ключу со столбцом ProductCategoryID выбранной таблицы ProductCategory.

> [!NOTE]
> Обратите внимание на направление стрелки в древовидном представлении связей.  

Для получения дополнительных сведений, таких как полное имя столбца, наведите на него указатель мыши, и появится всплывающее окно, как на следующем рисунке: 

![Извлечение отношений в каталоге данных Azure](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Для включения связей между зарегистрированными ресурсами зарегистрируйте их повторно.

## <a name="next-steps"></a>Дальнейшие действия
- [Как управлять ресурсами данных](data-catalog-how-to-manage.md)