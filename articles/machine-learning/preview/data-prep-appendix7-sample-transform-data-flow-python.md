---
title: "Примеры преобразований потока данных, поддерживаемых в средстве подготовки данных службы машинного обучения Azure | Документация Майкрософт"
description: "В этой статье приведены примеры преобразований потока данных, поддерживаемых в средстве подготовки данных службы машинного обучения Azure."
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
ms.date: 09/11/2017
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Пример пользовательских преобразований потока данных (Python) 
В меню этот сценарий преобразования имеет название Transform Dataflow (Script) (Преобразовать поток данных (скрипт)). Перед ознакомлением с этим приложением прочтите статью [Расширения Python для подготовки данных](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Преобразование блока данных
### <a name="create-a-new-column-dynamically"></a>Динамическое создание столбца 
Динамически создает столбец (Город2) и сопоставляет несколько разных версий Сан-Франциско с одной в имеющемся столбце городов.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Добавление новых статистических выражений
Создает блок данных на основе первого и последнего статистических выражений, вычисляемых для столбца результатов, сгруппированного по столбцу "Категория_риска".
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Винсоризация столбца 
Изменяет данные в соответствии с формулой, снижая тем самым выбросы в столбце.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Преобразование потока данных
### <a name="fill-down"></a>Заполнение по направлению вниз 
Заполнение по направлению вниз выполняется на основе двух преобразований.
В этом случае предполагается, что данные выглядят так:


|Состояние         |City       |
|--------------|-----------|
|Вашингтон    |Редмонд    |
|              |Бельвью   |
|              |Иссакуа   |
|              |Сиэтл;    |
|Калифорния    |Лос-Анджелес|
|              |San Diego  |
|              |Сан-Хосе   |
|Техас         |Даллас     |
|              |Сан-Антонио|
|              |Хьюстон    |

Прежде всего создайте преобразование Add Column (Script) (Добавить столбец (скрипт)), содержащее следующий код:
```python
    row['State'] if len(row['State']) > 0 else None
```
Теперь создайте преобразование Transform Dataflow (Script) (Преобразовать поток данных (скрипт)), содержащее следующий код:
```python
    df = df.fillna( method='pad')
```

Теперь данные выглядят так:

|Состояние         |Новое_состояние         |City       |
|--------------|--------------|-----------|
|Вашингтон    |Вашингтон    |Редмонд    |
|              |Вашингтон    |Бельвью   |
|              |Вашингтон    |Иссакуа   |
|              |Вашингтон    |Сиэтл;    |
|Калифорния    |Калифорния    |Лос-Анджелес|
|              |Калифорния    |San Diego  |
|              |Калифорния    |Сан-Хосе   |
|Техас         |Техас         |Даллас     |
|              |Техас         |Сан-Антонио|
|              |Техас         |Хьюстон    |

