---
title: "Примеры целевых и выходных форматов, поддерживаемых в средстве подготовки данных службы \"Машинное обучение Azure\" | Документация Майкрософт"
description: "В этой статье приведены примеры пользовательских целевых и выходных форматов, поддерживаемых в средстве подготовки данных службы \"Машинное обучение Azure\"."
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
ms.openlocfilehash: 4cad3343461a6c7eda78566b3d2552b1e3591960
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-destination-connections-python"></a>Пример подключений к местам назначений (Python) 
Перед ознакомлением с этим приложением см. [общие сведения о расширяемости Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Запись в Excel 


Для записи данных в Excel требуется дополнительная библиотека. Добавление новых библиотек описано в общих сведениях о расширяемости. `openpyxl` — это библиотека, которую требуется добавить.

Перед записью данных в Excel, возможно, потребуется внести некоторые другие изменения. В некоторых целевых форматах поддерживаются не все типы данных, используемые средством подготовки данных. Например, ошибочные значения не сериализируются должным образом в Excel. В этом случае для записи в Excel необходимо выполнить преобразование "Замена ошибочных значений", которое удаляет эти значения из всех столбцов.

Если вы уже выполнили описанные выше действия, используйте следующий код для записи таблицы данных на отдельный лист в документе Excel. Добавьте преобразование "Запись потока данных (скрипт)". Затем введите в разделе выражения следующий код:


### <a name="on-windows"></a>В Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>macOS и OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
