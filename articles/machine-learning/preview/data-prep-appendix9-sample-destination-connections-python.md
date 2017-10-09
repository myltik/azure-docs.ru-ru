---
title: "Примеры мест назначения данных и выходных данных, поддерживаемых в средстве подготовки данных службы машинного обучения Azure | Документация Майкрософт"
description: "В этой статье приведены примеры пользовательских мест назначения данных и выходных данных, поддерживаемых в средстве подготовки данных службы машинного обучения Azure."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---


# <a name="sample-of-destination-connections-python"></a>Пример подключений к местам назначений (Python) 
Перед ознакомлением с этим приложением прочтите статью [Расширения Python для подготовки данных](data-prep-python-extensibility-overview.md).


### <a name="write-to-excel"></a>Запись в Excel 


Чтобы записать данные в Excel, требуется дополнительная библиотека. Сведения о ее добавлении см. в статье о расширении для подготовки данных. `openpyxl` — это библиотека, которую требуется добавить.

Перед записью, возможно, потребуется внести некоторые другие изменения. Не все типы данных, используемые средством подготовки данных, поддерживаются в некоторых целевых форматах. Например, ошибочные значения не сериализируются должным образом в Excel. В этом случае преобразование Replace Error Values (Замена ошибочных значений) удаляет эти значения из всех столбцов, а затем выполняется запись в Excel.

На этом этапе подразумевается, что вы уже выполнили описанные выше действия. Пример кода ниже записывает таблицу данных на отдельный лист в документе Excel. Добавьте преобразование Write Dataflow (Script) (Запись потока данных (скрипт)) и введите в разделе выражения следующий код:


#### <a name="on-windows"></a>В Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>macOS и OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```

