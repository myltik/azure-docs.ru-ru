---
title: Комплексное руководство по использованию подготовки данных в Машинном обучении Azure | Документация Майкрософт
description: В этом документе представлен обзор и сведения о способах устранения проблем с данными с помощью подготовки данных в Машинном обучении Azure
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9fc0ae8929f12447123321a5c64ac89309c68dca
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="data-preparations-user-guide"></a>Руководство пользователя по подготовке данных 
Процесс подготовки данных в Машинном обучении Azure обеспечивает ряд расширенных возможностей, которые подробно описаны в разделах ниже.

### <a name="step-execution-history-and-caching"></a>Выполнение действий, журнал и кэширование 
Журнал действий по подготовке данных поддерживает ряд кэшей для повышения производительности. Если вы выберете действие и оно попадет в кэш, его нельзя будет выполнить повторно. Если в конце журнала действий имеется блок записи и если возвращаться к действиям, не выполняя при этом никаких изменений, то после первого выполнения запись не активируется. В этих случаях появится новая запись, которая перезапишет предыдущую, если:

- внести изменения в блок записи;
- добавить новый блок преобразования и переместить его выше блока записи, что приводит к недействительности кэша;
- изменить свойства этого блока выше блока записи,
- а также выбрать обновление образца (что сделает недействительными кэши).

### <a name="error-values"></a>Ошибочные значения

Преобразование данных может завершиться ошибкой для входного значения из-за его неправильной обработки. Например, операция приведения типа завершается сбоем, если значение строки ввода нельзя привести к указанному типу целевого объекта. Операция приведения типа должна преобразовывать столбец строкового типа в числовой либо логический тип или попытаться дублировать несуществующий столбец. (Этот сбой происходит в результате перемещения операции *удаления столбца X* перед выполнением операции *копирования столбца X*).

В этих случаях в выходных данных вы увидите ошибочное значение. Ошибочные значения указывают, что предыдущая операция для данного значения завершилась сбоем. Изнутри их можно рассматривать как тип значения первого класса, но их наличие не влияет на базовый тип столбца, даже если столбец включает только ошибочные значения.

Ошибочные значения легко определить. Они выделены красным цветом и помечаются как "Ошибка". Чтобы определить причину ошибки, наведите указатель мыши на ее значение, и вы увидите описание сбоя.

Ошибочные значения распространяются. После появления этого значения оно распространяется как ошибка в большинстве операций. Заменить или удалить их можно тремя способами:

* Замените
    -  Щелкните правой кнопкой мыши столбец и выберите **Replace Error Values** (Заменить ошибочные значения). Затем можно выбрать значение для замены для каждого ошибочного значения в столбце.

* Удалить
    - Подготовка данных включает интерактивные фильтры, чтобы сохранить или удалить ошибочные значения.
    - Щелкните правой кнопкой мыши столбец и выберите **Filter Column** (Фильтровать столбец). Чтобы сохранить или удалить ошибочные значения, создайте условие *is error* (является ошибкой) или *is not error* (не является ошибкой).

* Используйте выражение Python для условной обработки ошибочных значений. Дополнительные сведения см. в статье [Расширения Python для подготовки данных](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Выборка
Файл источников данных принимает необработанные данные из одного или нескольких источников либо из локальной файловой системы или удаленного расположения. Блок образцов позволяет указать, когда необходимо работать с подмножеством данных путем создания образцов. Работа с образцами данных, а не большим набором данных, улучшает производительность при выполнении последующих операций.

Для каждого файла источников данных можно создать и сохранить несколько образцов. Однако только один образец можно задать в качестве активного. Вы можете создавать, изменять или удалять образцы в мастере источников данных или путем изменения блока образцов. Любой файл подготовки данных, который изначально содержит ссылку на источник данных, использует образец, указанный в файле источников данных.

Есть ряд доступных стратегий выборки, каждая из которых содержит различные настраиваемые параметры.

#### <a name="top"></a>Первые
Эту стратегию можно применить к локальным или удаленным файлам. В источнике данных задействуются первые N строк (как указано в счетчике).

#### <a name="random-n"></a>Случайное количество 
Эту стратегию можно применить только к локальным файлам. В источнике данных задействуются случайные N строк (как указано в счетчике). Для создания того же образца можно указать конкретное начальное значение (в счетчике также должно быть указано прежнее значение).

#### <a name="random-"></a>Случайные, % 
Эту стратегию можно применить к локальным или удаленным файлам. В этих случаях необходимо указать вероятность и начальное значение, как в стратегии случайного количества.

Для образцов удаленных файлов необходимо указать дополнительные параметры.

- Генератор образцов 
  - Выберите кластер Spark или удаленный объект вычислений Docker для создания образцов. Объект вычислений необходимо создать для проекта заранее, чтобы он появился в этом списке. Чтобы создать объекты вычислений, ознакомьтесь с соответствующим разделом в статье [Как использовать GPU в службе "Машинное обучение Azure"](how-to-use-gpu.md).
- Хранилище образцов 
  - Укажите промежуточное расположение хранилища для хранения удаленных образцов. Этот путь должен представлять другой каталог из расположения входного файла.

#### <a name="full-file"></a>Весь файл 
Эту стратегию можно применить только к локальным файлам. В источник данных перемещается полный файл. Если файл слишком большой, использование этого параметра может привести к замедленному выполнению последующих операций в приложении. Поэтому можно рассмотреть вариант использования другой стратегии выборки.


### <a name="fork-merge-and-append"></a>Разветвление, слияние и добавление

При применении фильтра к набору данные разбиваются на два результирующих набора: один представляет записи без сбоев, а второй — записи, завершившиеся ошибкой. В любом случае пользователь может выбрать результирующий набор для отображения. Он также может отменить другой набор данных или поместить его в новый поток данных. Второй вариант известен как разветвление.

Чтобы выполнить разветвление: 
1. Выберите столбец, щелкните его правой кнопкой мыши и выберите столбец **Фильтр**.

2. Чтобы отобразить результирующий набор, который прошел фильтр, в разделе **Желаемое действие** выберите **Сохранить строки**.

3. Выберите **Удалить строки**, чтобы отобразить набор со сбоями.

4. После раздела **условий** выберите **создание потока данных с отфильтрованными строками** для разветвления набора, который не отобразился, в новый поток данных.


Такой подход чаще всего применяется для разделения набора данных, который требует дополнительной подготовки. После подготовки разветвленного набора данных можно объединить эти данные с результирующим набором в исходном потоке данных. Чтобы выполнить "слияние" (действие, обратное от "разветвления"), выполните одно из действий ниже:

- **Добавьте строки**. Вертикально объедините два или более потоков данных (на уровне строки). 
- **Добавьте столбцы**. Горизонтально объедините два или более потоков данных (на уровне столбца).


>[!NOTE]
>Добавление столбцов завершится ошибкой в случае их конфликта.


После операции слияния исходный поток данных содержит ссылку на один или несколько потоков данных. В правом нижнем углу приложения под списком действий будет показано уведомление.


Чтобы выполнить любую операцию с потоком данных, на который приводится ссылка, родительский поток данных должен обновить образец, используемый из этого потока данных. В этом случае в правом нижнем углу диалоговое окно подтверждения заменит уведомление о ссылке на поток данных. В этом диалоговом окне содержится подтверждение, что вам необходимо обновить поток данных для синхронизации изменений с любыми зависимыми потоками данных.

### <a name="list-of-appendices"></a>Список приложений 
* [Поддерживаемые источники данных](data-prep-appendix2-supported-data-sources.md)  
* [Поддерживаемые преобразования](data-prep-appendix3-supported-transforms.md)  
* [Поддерживаемые инспекторы](data-prep-appendix4-supported-inspectors.md)  
* [Поддерживаемые назначения](data-prep-appendix5-supported-destinations.md)  
* [Образец выражений фильтра в Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Образцы выражений преобразования потока данных в Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Образец источников данных в Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Пример подключений к назначению в Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Пример преобразований столбцов в Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
