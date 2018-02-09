---
title: "Примеры выражений фильтра, поддерживаемых в средстве подготовки данных службы \"Машинное обучение Azure\" | Документация Майкрософт"
description: "В этой статье приведены примеры выражений фильтра, поддерживаемых в средстве подготовки данных службы \"Машинное обучение Azure\""
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: df78e871625f4de406de5ba4ae0fea327b87b01e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-filter-expressions-python"></a>Пример выражений фильтра (Python) 
Перед ознакомлением с этим приложением см. [общие сведения о расширяемости Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Фильтрация с помощью теста на эквивалентность
Фильтрация только в тех строках, где значение Col2 (числовое) больше 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Фильтрация по нескольким столбцам 
Фильтрация только в тех строках, где свойство Col1 имеет значение **Good**, а Col2 — значение 1 (числовое). 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Проверка фильтров на наличие значения Null
Фильтрация только в тех строках, где свойство Col1 имеет значение Null. 
```python
    pd.isnull(row["Col1"])
```
