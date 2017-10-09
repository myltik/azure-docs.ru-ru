---
title: "Пример кода Python для создания столбцов с помощью средства подготовки данных в службе машинного обучения Azure | Документация Майкрософт"
description: "В этой статье приведены примеры кода Python, позволяющие создавать столбцы с помощью средства подготовки данных в службе машинного обучения Azure."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9a6e331e622b007232a62b34c2220d60d1d050ca
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-column-transforms-python"></a>Пример пользовательских преобразований столбцов (Python) 
В меню этот сценарий преобразования имеет название Add Column (Script) (Добавить столбец (скрипт))

Перед ознакомлением с этим приложением прочтите статью [Расширения Python для подготовки данных](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Проверка эквивалентности и замена значений 
Если значение Col1 меньше 4, тогда новый столбец должен иметь значение 1 (в противном случае — 2). 

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
Если свойство Col1 имеет значение Null, пометьте новый столбец как недопустимый (в противном случае — как допустимый). 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Новый вычисляемый столбец 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Вычисление эпохи 
Время в секундах с момента начала эпохи Unix (предполагается, что Col1 — дата). 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```






