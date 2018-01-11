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
ms.date: 09/11/2017
ms.openlocfilehash: ae70f5981c7ca533740968e0eea1b0b83df414fa
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
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
