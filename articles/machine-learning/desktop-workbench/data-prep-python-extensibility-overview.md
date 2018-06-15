---
title: Использование расширяемости Python с подготовкой данных в Машинном обучении Azure | Документация Майкрософт
description: В этом документе представлен обзор и некоторые подробные примеры использования кода Python для расширения функциональности подготовки данных
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.openlocfilehash: e91d3e4bfb1cba6f45b0106b3fabe5bef7e0e079
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831430"
---
# <a name="data-preparations-python-extensions"></a>Расширения Python для подготовки данных
В качестве способа заполнения функциональных пробелов между встроенными функциями подготовка данных в Машинном обучении Azure включает в себя расширяемость на нескольких уровнях. В этом документе описана расширяемость с помощью скрипта Python. 

## <a name="custom-code-steps"></a>Использование пользовательского кода 
Подготовка данных включает в себя следующие настраиваемые действия, когда пользователи могут писать код:

* Добавление столбца.
* Применение расширенного фильтра.
* Преобразование потока данных.
* Преобразование секции.

## <a name="code-block-types"></a>Типы блоков кода 
Для каждого из этих действий поддерживаются два типа блоков кода. Во-первых, поддерживаются чистые выражения Python, которые выполняются "как есть". Во-вторых, поддерживается модуль Python, в котором вызываются определенные функции с помощью известных подписей в коде, которые вам нужно предоставить.

Например, можно добавить новый столбец, который вычисляет журнал другого столбца двумя приведенными ниже способами:

Expression 

```python    
    math.log(row["Score"])
```

модуль 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Преобразование добавления столбца в модульном режиме предполагает поиск функции с именем `newvalue`, которая принимает переменную строки и возвращает значение для столбца. Этот модуль может содержать любое количество кода Python, в том числе другие функции, операции импорта и т. д.

В следующих разделах рассматриваются сведения о каждой точке расширения. 

## <a name="imports"></a>Импорт 
Если вы используете тип блока Expression (Выражение), вы можете добавить операторы **import** в код. Их необходимо сгруппировать в верхних строках кода.

Верно 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Ошибка  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Если вы используете тип блока Module (Модуль), вы можете следовать всем нормальным правилам Python для использования оператора **import**. 

## <a name="default-imports"></a>Операции импорта по умолчанию
Следующие операции импорта всегда включены и могут использоваться в коде. Их не требуется повторно импортировать. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Установка новых пакетов
Чтобы использовать пакет, не установленный по умолчанию, необходимо сначала установить его в средах, которые используются для подготовки данных. Эту установку необходимо выполнить как на локальном компьютере, так и на любых объектах вычисления, которые вы будете использовать.

Чтобы установить пакеты на объекте вычисления, необходимо изменить файл conda_dependencies.yml, расположенный в папке aml_config в корневом каталоге проекта.

### <a name="windows"></a>Windows 
Чтобы найти расположение в Windows, найдите установку для конкретного приложения Рython и ее каталог со скриптами. Расположение по умолчанию:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Затем выполните одну из следующих команд: 

`conda install <libraryname>` 

или 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Чтобы найти расположение в Mac, найдите установку для конкретного приложения Рython и ее каталог со скриптами. Расположение по умолчанию: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Затем выполните одну из следующих команд: 

`./conda install <libraryname>`

или 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Использование пользовательских модулей
В блоке Transform Dataflow (Script) напишите следующий код Python:

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

В блоке Add Column (Script) задайте для типа блока кода значение Module и напишите следующий код Python

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Для различных контекстов выполнения (локальная среда, Docker, Spark) укажите абсолютный путь к нужному расположению. Для его поиска можно использовать os.getcwd() + relativePath.


## <a name="column-data"></a>Данные столбцов 
Доступ к данным столбцов можно получить из строки, используя точечную нотацию или нотацию "ключ — значение". Имена столбцов, содержащие пробелы или специальные символы, нельзя получить с помощью точечной нотации. Переменная `row` всегда должна быть определена в обоих режимах расширений Рython (Module и Expression). 

Примеры 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Добавление столбца. 
### <a name="purpose"></a>Назначение
Точка расширения "Добавление столбца" позволяет записать код Python для вычисления нового столбца. Написанный код имеет доступ к целой строке. Он должен возвращать новое значение столбца для каждой строки. 

### <a name="how-to-use"></a>Использование
Эту точку расширения можно добавить с помощью блока Аdd Column (Script) (Добавление столбца (скрипт)). Он доступен как в меню верхнего уровня **Transformations** (Преобразования), так и в контекстном меню **Столбец**. 

### <a name="syntax"></a>Синтаксис
Expression

```python
    math.log(row["Score"])
```

модуль 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Применение расширенного фильтра.
### <a name="purpose"></a>Назначение 
Точка расширения "Настраиваемый фильтр" позволяет записать настраиваемый фильтр. У вас есть доступ ко всей строке, и код должен возвращать значение True (включить строку) или False (исключить строку). 

### <a name="how-to-use"></a>Использование
Эту точку расширения можно добавить с помощью блока Advanced Filter (Script) (Расширенный фильтр (скрипт)). Он доступен в меню верхнего уровня **Transformations** (Преобразования). 

### <a name="syntax"></a>Синтаксис

Expression

```python
    row["Score"] > 95
```

модуль  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Преобразование потока данных.
### <a name="purpose"></a>Назначение 
Точка расширения "Преобразование потока данных" позволяет полностью преобразовать поток данных. Имеется доступ к таблице данных Pandas, содержащей все обрабатываемые столбцы и строки. Код должен вернуть таблицу Pandas с новыми данными. 

>[!NOTE]
>При использовании этого расширения в Python все данные загружаются в память в таблице данных Pandas. 
>
>В Spark все данные собираются на одном рабочем узле. Если объем данных слишком большой, у рабочей роли может быть недостаточно памяти. Используйте этот способ осторожно.

### <a name="how-to-use"></a>Использование 
Эту точку расширения можно добавить с помощью блока Transform Dataflow (Script) (Преобразование потока данных (скрипт)). Он доступен в меню верхнего уровня **Transformations** (Преобразования). 
### <a name="syntax"></a>Синтаксис 

Expression

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

модуль 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Преобразование секции.  
### <a name="purpose"></a>Назначение 
Точка расширения "Преобразование секции" позволяет преобразовывать секции потока данных. Имеется доступ к таблице данных Pandas, содержащей все столбцы и строки этой секции. Код должен вернуть таблицу Pandas с новыми данными. 

>[!NOTE]
>В Python может формироваться одна или несколько секций в зависимости от размера данных. В Spark вы будете работать с таблицей данных, содержащей данные секции на определенном рабочем узле. В обоих случаях не следует рассчитывать на доступ ко всему набору данных. 


### <a name="how-to-use"></a>Использование
Эту точку расширения можно добавить с помощью блока Transform Partition (Script) (Преобразование секции (скрипт)). Он доступен в меню верхнего уровня **Transformations** (Преобразования). 

### <a name="syntax"></a>Синтаксис 

Expression 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

модуль 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Ошибочные значения  
В подготовке данных существует понятие значений ошибок. 

Вы можете столкнуться со значениями ошибок в пользовательском коде Рython. Они являются экземплярами класса Python с именем `DataPrepError`. Этот класс инкапсулирует исключение Python и имеет несколько свойств, содержащих сведения об ошибке, которая возникла при обработке исходного значения, а также исходное значение. 


### <a name="datapreperror-class-definition"></a>Определение класса DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Создание DataPrepError на платформе подготовки данных Рython обычно выглядит следующим образом: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Использование 
Python можно использовать, если он запущен в точке расширения, чтобы создать класс DataPrepErrors в качестве возвращаемых значений, используя описанный выше метод создания. Более вероятно, что DataPrepErrors будет обнаружен при обработке данных в точке расширения. На этом этапе пользовательский код Python должен обрабатывать DataPrepError как допустимый тип данных.

#### <a name="syntax"></a>Синтаксис 
Expression 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
модуль 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
