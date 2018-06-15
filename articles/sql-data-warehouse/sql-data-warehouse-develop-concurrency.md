---
title: Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL | Документация Майкрософт
description: Общие сведения управлении параллелизмом и рабочей нагрузкой в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: ''
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29959510"
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL
Чтобы обеспечить прогнозируемую и соответствующую масштабу производительность, хранилище данных SQL Microsoft Azure помогает пользователям управлять уровнями параллелизма и выделением ресурсов, например выделением памяти и определением приоритетов ЦП. В этой статье рассматриваются понятия параллелизма и управления рабочей нагрузкой и объясняется, каким образом реализованы эти функции и как управлять ими в хранилище данных. Управление рабочей нагрузкой в хранилище данных SQL обеспечивает поддержку многопользовательских сред, но не поддерживает мультитенантные рабочие нагрузки.

## <a name="concurrency-limits"></a>Пределы параллелизма
Хранилище данных SQL поддерживает до 1024 одновременных подключений. Все эти подключения могут одновременно отправлять запросы. Тем не менее, чтобы оптимизировать пропускную способность, хранилище данных SQL может помещать некоторые запросы в очередь. Это гарантирует, что на выполнение каждого запроса выделяется минимальный объем памяти. Постановка в очередь происходит во время выполнения запроса. При достижении пределов параллелизма хранилище данных SQL может повысить общую пропускную способность, обеспечивая активным запросам доступ к необходимым ресурсам памяти.  

Предельные границы параллелизма в хранилище данных SQL зависят от двух значений: *количества одновременных запросов* и *количества слотов выдачи*. Запрос выполняется, если не превышено пороговое значение количества одновременных запросов и достаточно выделенного количества слотов выдачи.

* Количество одновременных запросов — это количество запросов, выполняемых в одно и то же время. Хранилище данных SQL поддерживает до 32 одновременных запросов для больших значений DWU.
* Слоты выдачи выделяются на основе DWU. Каждые 100 DWU обеспечивают 4 слота. Например, для DW100 выделяется 4 слота, а для DW1000 — 40 слотов. Каждый запрос использует один или несколько слотов выдачи. Это зависит от [класса ресурсов](#resource-classes) запроса. Запросы, выполняемые с классом ресурсов smallrc, используют один слот выдачи. Запросы, выполняемые с более высоким классом ресурсов, будут использовать больше слотов выдачи.

В таблице ниже приведены ограничения для количества одновременных запросов и слотов выдачи для разных размеров DWU.

### <a name="concurrency-limits"></a>Пределы параллелизма
| DWU | Максимальное число одновременных запросов | Число выделенных слотов выдачи |
|:--- |:---:|:---:|
| DW100 |4. |4. |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

При достижении одного из этих пороговых значений, новые запросы помещаются в очередь и выполняются в порядке поступления.  Когда запрос выполняется и количество запросов и слотов уменьшается, запросы извлекаются из очереди. 

> [!NOTE]  
> *не* регулируются пороговыми значениями параллелизма. Вы можете следить за системой независимо от количества выполняемых в ней запросов.
> 
> 

## <a name="resource-classes"></a>Классы ресурсов
Классы ресурсов позволяют управлять выделением памяти и циклов ЦП для заданного запроса. Пользователю можно назначить два класса ресурсов в виде ролей базы данных. Существуют два типа классов ресурсов:
1. Классы динамических ресурсов (**smallrc, mediumrc, largerc, xlargerc**). Для них выделяется переменный объем памяти в зависимости от текущего значения DWU. Это означает, что при увеличении количества единиц использования хранилища данных (DWU) запросы автоматически получают больший объем памяти. 
2. Классы статических ресурсов (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**). Для них выделяется такой же объем памяти независимо от текущего значения DWU (при условии, что для самого значения DWU выделяется достаточный объем памяти). Это означает, что при большем значении DWU, можно выполнить дополнительные запросы в каждом классе ресурсов одновременно.

Для пользователей, использующих класс ресурсов **smallrc** и **staticrc10**, выделяется меньший объем памяти, и предоставляются большие возможности параллелизма. В то же время для пользователей, которым назначен класс ресурсов **xlargerc** или **staticrc80**, выделяется больший объем памяти, и, следовательно, меньше их запросов могут выполняться параллельно.

По умолчанию каждый пользователь является членом малого класса ресурсов — **smallrc**. Для повышения класса ресурсов используется процедура `sp_addrolemember`, а для уменьшения — `sp_droprolemember`. Например, следующая команда позволяет повысить класс ресурсов loaduser до класса **largerc**.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Запросы, которые не учитывают классы ресурсов

Существует несколько типов запросов, которые не смогут воспользоваться преимуществами большего выделения памяти. Система будет игнорировать их параметры выделения в соответствии с классом ресурсов и всегда будет выполнять эти запросы, применяя малый класс ресурсов. Если эти запросы выполняются в малом классе ресурсов, то они смогут выполняться при нехватке слотов выдачи и не будут использовать больше слотов, чем требуется. Дополнительные сведения см. в разделе [Исключения для запросов в отношении ограничений параллелизма](#query-exceptions-to-concurrency-limits).

## <a name="details-on-resource-class-assignment"></a>Дополнительные сведения о назначении классов ресурсов


Ниже приведены дополнительные сведения о классах ресурсов.

* *изменение роли* .
* Несмотря на то что можно добавить пользователя к одному или нескольким более высоким классам ресурсов, классы динамических ресурсов имеют приоритет над классами статических ресурсов. То есть, если пользователю назначаются **mediumrc**(динамический) и **staticrc80** (статический), учитывается класс ресурсов **mediumrc**.
 * Когда пользователю назначено более одного класса ресурсов в конкретном типе класса ресурсов (более одного класса динамических ресурсов или несколько классов статических ресурсов), будет учитываться более высокий класс ресурсов. Это означает, что если пользователю назначены классы ресурсов largerc и mediumrc, будет учитываться более высокий класс — largerc. Если пользователю назначены классы **staticrc20** и **statirc80**, будет учитываться класс **staticrc80**.
* Класс ресурсов для пользователя с правами системного администратора изменить нельзя.

Подробное описание примера см. в разделе [Пример изменения класса ресурсов пользователя](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Выделение памяти
Повышение класса ресурсов имеет свои преимущества и недостатки. Если повысить класс ресурсов для пользователя, для его запросов будет выделяться больший объем памяти, это означает, что они смогут выполняться быстрее.  Однако повышение класса ресурсов приводит к уменьшению количества одновременно выполняемых запросов. Нужно найти компромисс между выделением большого объема памяти для одного запроса и одновременным выполнением других запросов, для которых также требуется определенный объем памяти. Если одному пользователю выделено много памяти для запроса, то другие пользователи не будут иметь доступ к этой памяти для выполнения своих запросов.

В следующей таблице объем памяти, выделенный для каждого распределения, сопоставлен по количеству DWU и классу ресурсов.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Выделение памяти на распределение для классов динамических ресурсов (в МБ)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1 600 |
| DW500 |100 |400 |800 |1 600 |
| DW600 |100 |400 |800 |1 600 |
| DW1000 |100 |800 |1 600 |3200 |
| DW1200 |100 |800 |1 600 |3200 |
| DW1500 |100 |800 |1 600 |3200 |
| DW2000 |100 |1 600 |3200 |6400 |
| DW3000 |100 |1 600 |3200 |6400 |
| DW6000 |100 |3200 |6400 |12 800 |

В следующей таблице объем памяти, выделенный для каждого распределения, сопоставлен по количеству DWU и классу статических ресурсов. Обратите внимание, что объем памяти для более высокого класса ресурсов уменьшается для учета глобальных ограничений DWU.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Выделение памяти на распределение для классов статических ресурсов (в МБ)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1 600 |1 600 |1 600 |1 600 |
| DW500 |100 |200 |400 |800 |1 600 |1 600 |1 600 |1 600 |
| DW600 |100 |200 |400 |800 |1 600 |1 600 |1 600 |1 600 |
| DW1000 |100 |200 |400 |800 |1 600 |3200 |3200 |3200 |
| DW1200 |100 |200 |400 |800 |1 600 |3200 |3200 |3200 |
| DW1500 |100 |200 |400 |800 |1 600 |3200 |3200 |3200 |
| DW2000 |100 |200 |400 |800 |1 600 |3200 |6400 |6400 |
| DW3000 |100 |200 |400 |800 |1 600 |3200 |6400 |6400 |
| DW6000 |100 |200 |400 |800 |1 600 |3200 |6400 |12 800 |

Как видно из представленной выше таблицы, для запроса, который выполняется при DW2000 с классом ресурсов **xlargerc**, в каждой из 60 распределенных баз данных выделяется по 6400 МБ памяти.  В хранилище данных SQL используется 60 распределений. Таким образом, чтобы вычислить, сколько памяти в целом выделяется на запросы в указанном классе ресурсов, приведенные выше значения следует умножить на 60.

### <a name="memory-allocations-system-wide-gb"></a>Выделение памяти для всей системы (ГБ)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Как видно из таблицы выделения памяти для всей системы, для запроса, который выполняется при DW2000 с классом ресурсов xlargerc, всего выделяется 375 ГБ памяти (6400 МБ * 60 распределений/1024 (чтобы преобразовать в ГБ)) во всем хранилище данных SQL.

Такой же расчет применяется к классам статических ресурсов.
 
## <a name="concurrency-slot-consumption"></a>Использование слотов выдачи  
Хранилище данных SQL предоставляет больше памяти запросам, выполняемым при более высоких классах ресурсов. Память — фиксированный ресурс.  Таким образом чем больше памяти выделяется на один запрос, тем меньше параллельных запросов можно выполнять. В следующей таблице все вышесказанное сведено в одно представление. В нем приведено количество слотов выдачи, доступное для определенного значения DWU, а также показатель их использования по классам ресурсов.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Выделение и использование слотов параллельности для классов динамических ресурсов  
| DWU | Максимальное число одновременных запросов | Число выделенных слотов выдачи | Слоты, используемые smallrc | Слоты, используемые mediumrc | Слоты, используемые largerc | Слоты, используемые xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4. |4. |1 |1 |2 |4. |
| DW200 |8 |8 |1 |2 |4. |8 |
| DW300 |12 |12 |1 |2 |4. |8 |
| DW400 |16 |16 |1 |4. |8 |16 |
| DW500 |20 |20 |1 |4. |8 |16 |
| DW600 |24 |24 |1 |4. |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Выделение и использование слотов выдачи для классов статических ресурсов  
| DWU | Максимальное число одновременных запросов | Число выделенных слотов выдачи |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4. |4. |1 |2 |4. |4. |4. |4. |4. |4. |
| DW200 |8 |8 |1 |2 |4. |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4. |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4. |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4.| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4.| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4.| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4.| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4.| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4.| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4.| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4.| 8| 16| 32| 64| 128|

В этих таблицах можно увидеть, что хранилище данных SQL при использовании DW1000 обрабатывает до 32 параллельных запросов и может выделить 40 слотов выдачи. Если все пользователи работают с классом smallrc, то будет разрешено выполнять 32 параллельных запроса, так как каждый запрос использует 1 слот выдачи. Если бы все пользователи при использовании DW1000 работали с классом mediumrc, то для каждого запроса было бы выделено по 800 МБ на распределение, то есть всего было бы выделено 47 ГБ памяти на запрос, и параллельно работать смогли бы только 5 таких пользователей (40 слотов выдачи / 8 слотов на пользователя с классом ресурсов mediumrc).

## <a name="selecting-proper-resource-class"></a>Выбор правильного класса ресурсов  
Рекомендуется сразу назначать пользователям постоянный класс ресурсов и избегать его изменения. Например, если выделен большой объем памяти, то во время добавления данных в кластеризованные таблицы columnstore создаются индексы более высокого качества. Чтобы для операций добавления данных выделялся большой объем памяти, необходимо создать пользователя, предназначенного для этой операции, и назначить ему более высокий класс ресурсов.
Вот несколько рекомендаций. Как упоминалось выше, хранилище данных SQL поддерживает два вида типов классов ресурсов: классы статических ресурсов и классы динамических ресурсов.
### <a name="loading-best-practices"></a>Рекомендации по загрузкам
1.  Если ожидаются загрузки с обычным объемом данных, следует выбрать класс статических ресурсов. Позже, при вертикальном масштабировании для получения большей вычислительной мощности, хранилище данных сможет изначально выполнять несколько параллельных запросов, так как пользователь загрузки не потребляет больший объем памяти.
2.  Если ожидаются высокие объемы загрузки в некоторых случаях, следует выбрать класс динамических ресурсов. Позже, при масштабировании для получения большей вычислительной мощности, пользователь загрузки получит больше готовой памяти, что позволит ускорить выполнение загрузки.

Объем памяти, необходимый для обработки загрузки, напрямую зависит от природы загружаемой таблицы и объема обрабатываемых данных. Например, для загрузки данных в таблицы CCI требуется определенный объем памяти, чтобы достичь оптимальных групп строк CCI. Дополнительные сведения см. в статье "Индексы columnstore. Руководство по загрузке данных".

Рекомендуется использовать не менее 200 МБ памяти для загрузки.

### <a name="querying-best-practices"></a>Рекомендации по выполнению запросов
Запросы имеют различные требования в зависимости от их сложности. Увеличение объема памяти на запрос или увеличение параллелизма являются допустимыми способами дополнения общей пропускной способности в соответствии с требованиями запроса.
1.  Если ожидаются обычные, сложные запросы (к примеру, для формирования ежедневных и еженедельных отчетов) и нет необходимости использовать преимущества параллелизма, следует использовать класс динамических ресурсов. Если в системе имеется больший объем данных для обработки, тогда вследствие вертикального масштабирования хранилища данных пользователь, выполняющий запрос, автоматически получит больший объем памяти.
2.  Если ожидаются переменные или суточные шаблоны параллелизма (например, если база данных запрашивается через широкодоступный веб-интерфейс), следует выбрать класс статических ресурсов. Позже, при масштабировании до хранилища данных, пользователь, связанный с классом статических ресурсов, автоматически сможет выполнять большее количество параллельных запросов.

Выбор правильного временно предоставляемого буфера памяти на основе потребности запроса является нетривиальным, так как он зависит от многих факторов, таких как количество запрашиваемых данных, характер схем таблиц, различные предикаты соединения, выделения и группирования. С общей точки зрения, выделение большего объема памяти позволит ускорить выполнение запросов, но понизит общий уровень параллелизма. Если низкий уровень параллелизма не является проблемой, чрезмерное выделение памяти не нанесет вреда. Для точной настройки пропускной способности можно попробовать использовать различные виды классов ресурсов.

Вы можете использовать следующую хранимую процедуру, чтобы определить уровень параллелизма и выделения памяти для каждого класса ресурсов при заданном значении SLO и ближайший наилучший класс ресурсов для операций CCI с высоким объемом требуемой памяти в несекционированной таблице CCI в данном классе ресурсов:

#### <a name="description"></a>Описание:  
Ниже представлено предназначение этой хранимой процедуры.  
1. Помочь пользователю определить необходимый уровень параллелизма и выделения памяти на класс ресурса при заданном значении SLO. Для этого пользователь должен предоставить значение NULL для параметра schema и tablename, как показано в следующем примере.  
2. Помочь определить ближайший наилучший класс ресурсов для операций CCI с интенсивным использованием памяти (загрузка, копирование таблиц, перестроение индекса и т. д.) в несекционированной таблице CCI в данном классе ресурсов. Хранимая процедура использует схему таблицы, чтобы определить требуемый объем временно предоставляемого буфера памяти.

#### <a name="dependencies--restrictions"></a>Зависимости и ограничения
- Данная хранимая процедура не предназначена для вычисления требований к объему памяти для секционированной таблицы CCI.    
- Данная хранимая процедура не учитывает требования к объему памяти для части SELECT оператора CTAS/INSERT-SELECT и предполагает, что это простой оператор SELECT.
- Данная хранимая процедура применяет временную таблицу для использования в сеансе, во время которого была создана данная хранимая процедура.    
- Данная хранимая процедура зависит от текущих предложений (например, конфигурации оборудования, конфигурации DMS), и если какое-либо из этих предложений изменится, тогда данная хранимая процедура будет работать неправильно.  
- Данная хранимая процедура зависит от существующего предлагаемого ограничения параллелизма, и если он изменится, то данная хранимая процедура будет работать неправильно.  
- Данная хранимая процедура зависит от существующих предложений класса ресурсов, и если они изменится, то данная хранимая процедура будет работать неправильно.  

>  [!NOTE]  
>  Если вы не получаете выходные данные после выполнения хранимой процедуры с предоставленными параметрами, тогда возможны два варианта. <br />1. Один из параметров хранилища данных содержит недопустимое значение SLO. <br />2. Отсутствует соответствующий класс ресурсов для операции CCI, если было указано имя таблицы. <br />Например, на уровне DW100 максимально доступный объем временно предоставляемого буфера памяти составляет 400 МБ, если схема таблицы достаточно широка, чтобы достигнуть такого показателя.
      
#### <a name="usage-example"></a>Пример использования
Синтаксис:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: укажите параметр NULL для извлечения текущего значения DWU из базы данных хранилища данных или укажите любые поддерживаемые значения DWU в форме DW100.
2. @SCHEMA_NAME: введите имя схемы таблицы.
3. @TABLE_NAME: введите имя нужной таблицы.

Примеры выполнения этой хранимой процедуры.  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Таблицу Table1, используемую в приведенных выше примерах, можно создать так:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Определение хранимой процедуры:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Приоритет при выполнении запросов
Классы ресурсов в хранилище данных SQL реализованы с помощью групп рабочей нагрузки. Для управления поведением классов ресурсов для различных размеров DWU используются восемь групп рабочей нагрузки. Для любого значения DWU хранилище данных SQL использует только четыре группы рабочей нагрузки из восьми. Это связано с тем, что каждая группа рабочей нагрузки назначается только одному из четырех классов ресурсов: smallrc, mediumrc, largerc или xlargerc. Этот аспект важно учитывать, так как некоторым группам рабочей нагрузки назначается более высокий *приоритет*. Значение приоритета используется при планировании распределения ресурсов ЦП. Запросы, которым назначен высокий приоритет, получают в три раза больше циклов ЦП, чем запросы со средним приоритетом. Поэтому сопоставление слотов выдачи также определяет приоритет ЦП. Если для выполнения запроса используется 16 или больше слотов, он помечается как запрос с высоким приоритетом.

В таблице ниже приведены сведения о приоритете для каждой группы рабочей нагрузки.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Сопоставление групп рабочей нагрузки со слотами выдачи и приоритетами
| Группы рабочей нагрузки | Сопоставление слотов выдачи | МБ/распределение | Приоритет |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Средний |
| SloDWGroupC01 |2 |200 |Средний |
| SloDWGroupC02 |4. |400 |Средний |
| SloDWGroupC03 |8 |800 |Средний |
| SloDWGroupC04 |16 |1 600 |Высокий |
| SloDWGroupC05 |32 |3200 |Высокий |
| SloDWGroupC06 |64 |6400 |Высокий |
| SloDWGroupC07 |128 |12 800 |Высокий |

На диаграмме **Выделение и использование слотов выдачи** видно, что DW500 использует 1, 4, 8 или 16 слотов выдачи для классов smallrc, mediumrc, largerc и xlargerc, соответственно. Вы можете ознакомиться с этими значениями на предыдущей диаграмме, чтобы узнать приоритет каждого класса ресурсов.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Сопоставление классов ресурсов с приоритетами для DW500
| Класс ресурсов | Группа рабочей нагрузки | Число используемых слотов выдачи | МБ/распределение | приоритет |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Средний |
| mediumrc |SloDWGroupC02 |4. |400 |Средний |
| largerc |SloDWGroupC03 |8 |800 |Средний |
| xlargerc |SloDWGroupC04 |16 |1 600 |Высокий |
| staticrc10 |SloDWGroupC00 |1 |100 |Средний |
| staticrc20 |SloDWGroupC01 |2 |200 |Средний |
| staticrc30 |SloDWGroupC02 |4. |400 |Средний |
| staticrc40 |SloDWGroupC03 |8 |800 |Средний |
| staticrc50 |SloDWGroupC03 |16 |1 600 |Высокий |
| staticrc60 |SloDWGroupC03 |16 |1 600 |Высокий |
| staticrc70 |SloDWGroupC03 |16 |1 600 |Высокий |
| staticrc80 |SloDWGroupC03 |16 |1 600 |Высокий |

С помощью приведенного ниже запроса к динамическому административному представлению можно увидеть различия в распределении ресурсов памяти с точки зрения регулятора ресурсов. Этот запрос также подходит для анализа текущего и предыдущего использования групп рабочей нагрузки при устранении неполадок.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Запросы, учитывающие пределы параллелизма
Большинство запросов управляются классами ресурсов. Эти запросы не должны выходить за пороговые значения количества параллельных запросов и слотов выдачи. Пользователь не может самостоятельно исключить запрос из модели слотов выдачи.

Обратите внимание, что приведенные ниже инструкции учитывают классы ресурсов:

* INSERT SELECT
* UPDATE
* УДАЛИТЬ
* SELECT (при запросе таблиц пользователя)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Загрузка данных
* Операции перемещения данных, осуществляемые службой Data Movement Service (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Исключения для запросов в отношении ограничений параллелизма
Некоторые запросы не учитывают класс ресурсов, которой назначен пользователю. Эти исключения в отношении ограничения параллелизма допускаются, когда не хватает ресурсов памяти, нужных для выполнения определенной команды, так как нередко команда является операцией с метаданными. Такие исключения позволяют избежать выделения большого объема памяти для запросов, которым она никогда не понадобится. В таких случаях используется класс небольших ресурсов по умолчанию (smallrc), независимо от того, какой класс ресурсов назначен пользователю. Например, `CREATE LOGIN` всегда будет работать с классом smallrc. Для выполнения этой задачи не требуются значительные ресурсы, поэтому нет смысла включать этот запрос в модель слотов выдачи.  Эти запросы также не ограничивается пределом в 32 одновременных запроса на пользователя. Может быть выполнено неограниченное количество этих запросов в пределах максимального числа сеансов, равного 1024.

Приведенные ниже инструкции не учитывают классы ресурсов:

* CREATE или DROP TABLE;
* ALTER TABLE ... SWITCH, SPLIT или MERGE PARTITION;
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE или DROP STATISTICS;
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER или DROP USER;
* CREATE, ALTER или DROP PROCEDURE;
* CREATE или DROP VIEW;
* INSERT VALUES
* SELECT (из системных представлений и динамических административных представлений);
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> Пример изменения класса ресурсов пользователя
1. **Создание имени для входа.** Откройте подключение к базе данных **master** на сервере SQL Server, на котором размещена база данных хранилища данных SQL, и выполните следующие команды.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Рекомендуется создать в базе данных master учетную запись для пользователей хранилища данных SQL Azure. Это позволит пользователям входить в систему с помощью таких инструментов, как среда SSMS, без указания имени базы данных.  Кроме того, это позволяет использовать обозреватель объектов для просмотра всех баз данных в SQL Server.  Дополнительные сведения о создании пользователей и управлении ими см. в статье [Защита базы данных в хранилище данных SQL][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Создание учетной записи пользователя хранилища данных SQL.** Откройте подключение к базе данных **хранилища данных SQL** и выполните следующую команду.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Предоставление разрешений.** В следующем примере предоставляются права `CONTROL` над базой данных **хранилища данных SQL**. `CONTROL` на уровне базы данных соответствует разрешениям db_owner в SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Повышение класса ресурсов.** Чтобы добавить пользователя к роли управления более высокой рабочей нагрузкой, используйте следующий запрос.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Понижение класса ресурсов.** Чтобы удалить пользователя из роли управления рабочей нагрузкой, используйте следующий запрос.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Невозможно удалить пользователя из smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Представление, используемое для определения запросов, поставленных в очередь, и другие динамические административные представления
Определить запросы, попавшие в очередь параллельной обработки, можно с помощью динамического административного представления `sys.dm_pdw_exec_requests` . Запросы, ожидающие выделения слота выдачи, будет находиться в **приостановленном**состоянии.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Чтобы просмотреть роли управления рабочей нагрузкой, можно использовать представление `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

Следующий запрос позволяет определить, в какие роли добавлены пользователи.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

В хранилище данных SQL предусмотрены следующие типы ожиданий.

* **LocalQueriesConcurrencyResourceType**относится к запросам, которые не входят в платформу слотов выдачи. В качестве примеров таких запросов можно привести запросы и системные функции динамических административных представлений, такие как `SELECT @@VERSION` .
* **UserConcurrencyResourceType**относится к запросам, которые входят в платформу слотов выдачи. В качестве примеров использования этого типа ресурсов можно привести запросы к таблицам пользователя.
* **DmsConcurrencyResourceType**относится к ожиданиям, связанным с операциями перемещения данных.
* **BackupConcurrencyResourceType**может использоваться при создании резервной копии базы данных. Максимальное значение для этого типа ресурсов равно 1. При одновременном запросе сразу нескольких резервных копий все остальные запросы помещаются в очередь.

Определить, какие ресурсы необходимы для запроса, можно при помощи динамического административного представления `sys.dm_pdw_waits` .

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

Динамическое административное представление `sys.dm_pdw_resource_waits` показывает только время ожидания ресурса по конкретному запросу. Время ожидания ресурсов — это время, в течение которого запросы ожидают предоставления ресурсов, а время ожидания сигнала — время, необходимое базовому экземпляру SQL Server, чтобы спланировать выделение запросу ресурсов ЦП.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

Для анализа тенденций времени ожидания за прошедший период используется динамическое административное представление `sys.dm_pdw_wait_stats` .

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об управлении пользователями и безопасностью базы данных см. в статье [Защита базы данных в хранилище данных SQL][Secure a database in SQL Data Warehouse]. Дополнительные сведения о повышении качества кластеризованных индексов columnstore за счет повышения класса ресурсов см. в разделе [Повышение качества сегментов за счет перестроения индексов].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Повышение качества сегментов за счет перестроения индексов]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
