---
title: "Пример кода Python для создания столбцов с помощью средства подготовки данных в службе машинного обучения Azure | Документация Майкрософт"
description: "В этой статье приведены примеры кода Python для создания столбцов с помощью средства подготовки данных в службе машинного обучения Azure."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 143031ce804f4a8dcd4e328c413478f5ea669090
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-custom-column-transforms-python"></a>Пример пользовательских преобразований столбцов (Python) 
В меню этот сценарий преобразования имеет название **Добавление столбца (скрипт)**.

Перед ознакомлением с этим приложением см. [общие сведения о расширяемости Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Проверка эквивалентности и замена значений 
Если значение Col1 меньше 4, тогда новый столбец должен иметь значение 1. Если значение Col1 больше 4, тогда новый столбец должен иметь значение 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Текущая дата и время 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Преобразование типа 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Оценка наличия значений Null 
Если свойство Col1 имеет значение Null, отметьте новый столбец как **недопустимый**. В противном случае отметьте его как **допустимый**. 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Новый вычисляемый столбец 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Вычисление эпохи 
Время в секундах с момента начала эпохи Unix (предполагается, что Col1 — это дата): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```





