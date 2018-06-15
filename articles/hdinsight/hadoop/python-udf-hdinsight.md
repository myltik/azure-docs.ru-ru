---
title: Использование определяемых пользователем функций Python с Apache Hive и Pig в Azure HDInsight | Документация Майкрософт
description: Узнайте, как использовать пользовательские функции (UDF) технологической платформы Hadoop на базе Azure — Python с Hive и Pig в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.devlang: python
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: c508ffcdd5cccaad5c7b0717d33e2ea1449ab522
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405499"
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Использование пользовательских функций Python с Hive и Pig в Azure HDInsight

Узнайте, как использовать определяемые пользователем функции (UDF) Python с Apache Hive и Pig в Hadoop на кластерах Azure HDInsight.

## <a name="python"></a>Python в HDInsight

По умолчанию на кластерах HDInsight 3.0 и более поздних версиях установлен Python версии 2.7. Apache Hive можно использовать с этой версией Python для потоковой обработки. При этом для передачи данных между Hive и определяемой пользователем функцией используется STDOUT и STDIN.

В состав HDInsight также входят Jython, который представляет собой реализацию Python, написанную на Java. Jython выполняется непосредственно на виртуальной машине Java и не использует потоковую передачу. Jython является рекомендуемым интерпретатором Python при использовании Python с Pig.

> [!WARNING]
> Шаги в этом документе основаны на следующих предположениях: 
>
> * Вы создаете скрипты Python в локальной среде разработки.
> * Вы отправляете скрипты в HDInsight, используя либо команду `scp` из локального сеанса Bash либо предоставленный сценарий PowerShell.
>
> Если вы хотите использовать предварительную версию [Azure Cloud Shell (оболочка)](https://docs.microsoft.com/azure/cloud-shell/overview) для работы с HDInsight, вам необходимо:
>
> * Создать скрипты в среде Cloud Shell.
> * Использовать `scp` для отправки файлов из Cloud Shell в HDInsight.
> * Использовать `ssh` из Cloud Shell для подключения к HDInsight и выполнения примеров.

## <a name="hivepython"></a>Определяемая пользователем функция Hive

Скрипт Python можно использовать в качестве определяемой пользователем функции из Hive через HiveQL с помощью инструкции `TRANSFORM`. Например, следующий запрос HiveQL вызывает файл `hiveudf.py`, хранящийся в учетной записи хранения Azure по умолчанию для кластера.

**HDInsight под управлением Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight под управлением Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> В кластерах HDInsight под управлением Windows оператор `USING` должен задавать полный путь к python.exe.

Вот что делает данный пример:

1. Инструкция `add file` в начале файла добавляет файл `hiveudf.py` в распределенный кэш, и он становится доступен всем узлам кластера.
2. Инструкция `SELECT TRANSFORM ... USING` выбирает данные из `hivesampletable`. Она также передает параметры clientid, devicemake и devicemodel в скрипт `hiveudf.py`.
3. Предложение `AS` описывает поля, возвращаемые из `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Создание файла hiveudf.py


В среде разработки создайте текстовый файл с именем `hiveudf.py`. Используйте следующий код в качестве содержимого файла:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Сценарий выполняет следующие действия:

1. Чтение данных из STDIN.
2. Стоящий в конце знак новой строки удаляется с помощью `string.strip(line, "\n ")`.
3. При обработке потока в одной строке будут содержаться все значения, разделенные символом табуляции. Поэтому можно использовать `string.split(line, "\t")` для разделения входящих данных при каждой табуляции, возвращая лишь поля.
4. По завершении обработки результат должен быть записан в поток STDOUT в виде одной строки, с разделенными символами табуляции полями. Например, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Цикл `while` повторяется до тех пор, пока считывается `line`.

Выходные данные скрипта представляют собой объединенные входные значения для `devicemake` и `devicemodel`, а также хэш для объединенного значения.

Сведения о выполнении этого примера в кластере HDInsight см. в разделе [Выполнение примеров](#running).

## <a name="pigpython"></a>Определяемая пользователем функция Pig

Скрипт Python можно использовать в виде определяемой пользователем функции из Pig с использованием инструкции `GENERATE`. Вы можете запустить скрипт с помощью Jython или CPython.

* Jython работает на виртуальной машине Java и изначально может вызываться из Pig.
* CPython является внешним процессом, поэтому данные из Pig на JVM отправляются в скрипт, выполняющийся в процессе Python. Выходные данные скрипта Python отправляются обратно в Pig.

Чтобы указать интерпретатор Python, используйте `register` при указании ссылки на скрипт Python. Следующие примеры регистрируют скрипты с Pig в качестве `myfuncs`:

* **Для использования Jython:** `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Для использования CPython:** `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> При использовании Jython путь к файлу pig_jython может быть локальным путем или путем WASB://. Но при использовании CPython необходимо указать ссылку на файл в локальной файловой системе узла, который используется для отправки задания Pig.

После регистрации язык Pig Latin будет одинаковым для обоих примеров:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Вот что делает данный пример:

1. Первая строка загружает образец файла данных `sample.log` в `LOGS`. Она также определяет каждую запись как массив символов `chararray`.
2. Следующая строка отфильтровывает все пустые значения, сохраняя результат работы в `LOG`.
3. Затем выполняется итерация по записям в `LOG` и используется инструкция `GENERATE` для вызова метода `create_structure`, содержащегося в скрипте Python или Jython, загруженном как `myfuncs`. `LINE` используется для передачи текущей записи в функцию.
4. Наконец, выходные данные сбрасываются в поток STDOUT командой `DUMP`. После завершения операции эта команда выведет результат.

### <a name="create-the-pigudfpy-file"></a>Создание файла pigudf.py

В среде разработки создайте текстовый файл с именем `pigudf.py`. Используйте следующий код в качестве содержимого файла:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

В примере Pig Latin вход `LINE` определен в виде массива строк, потому что для него нет согласованной схемы. Скрипт Python выполняет преобразование данных в согласованную схему на выходе.

1. Инструкция `@outputSchema` задает формат данных, в котором они возвращаются в Pig. В данном случае это **data bag**, являющийся типом данных Pig. Корзина содержит следующие поля, все они имеют тип "Массив строк" (строки):

   * date — дата создания записи журнала;
   * date — время создания записи журнала;
   * classname — имя класса, для которого создана запись;
   * level — уровень журналирования;
   * detail — подробная информация о записи журнала.

2. Затем `def create_structure(input)` определяет функцию, в которую Pig отправляет строковые элементы.

3. Данные для примера, `sample.log`, в основном соответствуют схеме даты, времени, имени класса, уровня и подробной информации. Однако он содержит несколько строк, начинающихся с `*java.lang.Exception*`. Эти строки должны быть изменены в соответствии со схемой. Инструкция `if` проверяет на наличие таких строк, затем обрабатывает входные данные, переставляя строку `*java.lang.Exception*` в конец, формируя данные в соответствии с ожидаемой схемой.

4. Затем команда `split` используется для разделения данных по первым четырем символам пробела. Выходным данным присваиваются значения `date`, `time`, `classname`, `level` и `detail`.

5. И результаты возвращаются в Pig.

Когда данные возвращаются в Pig, они имеют согласованную схему, определенную инструкцией `@outputSchema`.

## <a name="running"></a>Отправка и выполнение примеров

> [!IMPORTANT]
> Действия с **SSH** работают только с кластером HDInsight на базе Linux. Действия с **PowerShell** работают с кластером HDInsight на базе Linux и Windows, но требуют клиента Windows.

### <a name="ssh"></a>SSH

Дополнительные сведения об использовании SSH см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Используйте `scp` для копирования файлов в кластер HDInsight. Например, следующая команда позволяет скопировать файлы в кластер с именем **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Используйте SSH, чтобы подключиться к кластеру.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. В сеансе SSH добавьте переданные ранее файлы Python в хранилище WASB для кластера.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

После передачи файлов выполните следующие действия для выполнения заданий Hive и Pig.

#### <a name="use-the-hive-udf"></a>Использование определяемой пользователем функции Hive

1. Подключитесь к Hive, выполнив следующую команду.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Эта команда запускает клиент Beeline.

2. Введите следующий запрос `0: jdbc:hive2://headnodehost:10001/>` в командной строке:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. После ввода последней строки запустится задание. По завершении задания эта команда возвращает выходные данные следующего вида:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Чтобы выйти из Beeline, выполните следующую команду.

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Использование определяемой пользователем функции Pig

1. Чтобы подключиться к pig, выполните следующую команду.

    ```bash
    pig
    ```

2. В окне запроса `grunt>` введите следующие операторы:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. После ввода указанной строки должно запуститься задание. По завершении задания эта команда возвращает выходные данные следующего вида:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Используйте `quit` для выхода из оболочки Grunt, а затем следующую команду для изменения файла pigudf.py в локальной файловой системе:

    ```bash
    nano pigudf.py
    ```

5. Войдите в редактор и раскомментируйте следующую строку, удалив символ `#` в начале строки.

    ```bash
    #from pig_util import outputSchema
    ```

    Эта строка изменяет сценарий Python для работы с C Python вместо Jython. Закончив вносить изменения, нажмите клавиши **CTRL+X**, чтобы выйти из редактора. Выберите **Y** (Да) и нажмите клавишу **ВВОД**, чтобы сохранить изменения.

6. Используйте команду `pig` , чтобы снова запустить оболочку. При появлении запроса `grunt>` введите следующие инструкции, чтобы запустить сценарий Python с помощью интерпретатора CPython.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Когда это задание будет выполнено, вы увидите такой же результат, как при запуске сценария с помощью Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: отправка файлов

Вы можете использовать PowerShell для отправки файлов на сервер HDInsight. Используйте следующий скрипт для отправки файлов Python:

> [!IMPORTANT] 
> В этом разделе используется Azure PowerShell. Дополнительные сведения об использовании Azure PowerShell см. в статье [How to install and configure Azure PowerShell](/powershell/azure/overview) (Как установить и настроить Azure PowerShell).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Измените значение `C:\path\to` на путь к файлам в среде разработки.

Этот скрипт получает информацию для кластера HDInsight, извлекает учетную запись и ключ для учетной записи хранения по умолчанию и загружает файлы в корневую папку контейнера.

> [!NOTE]
> Дополнительные сведения о загрузке файлов см. в статье [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md).

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: использование определяемой пользователем функции Hive

PowerShell также можно использовать для удаленного запуска запросов на использование Hive. Используйте следующий сценарий PowerShell для запуска запроса Hive, который использует скрипт **hiveudf.py**:

> [!IMPORTANT]
> Перед запуском он предлагает вам ввести сведения об HTTPS и учетной записи администратора для кластера HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

Результат выполнения задания **Hive** должен выглядеть аналогично следующему примеру:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell также можно использовать для запуска заданий Pig Latin. Для запуска задания Pig Latin, использующего скрипт **pigudf.py**, используйте следующий сценарий PowerShell:

> [!NOTE]
> При удаленной отправке задания с помощью PowerShell нельзя использовать CPython в качестве интерпретатора.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

Результат выполнения задания **Pig** должен выглядеть аналогично следующим данным:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="errors-when-running-jobs"></a>Ошибки при выполнении заданий

При выполнении задания hive может возникнуть ошибка, аналогичная приведенной ниже:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Эта проблема может быть вызвана символами окончания строк в файле Python. Многие редакторы Windows по умолчанию используют символы CRLF, но в приложениях Linux обычно ожидается использование символа LF.

Вы можете использовать следующие команды PowerShell для удаления символов CR перед передачей файла в HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Сценарии PowerShell

Оба примера скриптов PowerShell, используемых для запуска примеров, содержат закомментированную строку, которая отображает вывод ошибок для задания. Если вы не видите ожидаемых результатов задания, раскомментируйте следующую строку и просмотрите информацию об ошибках на предмет отображения проблемы.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Сведения об ошибках (STDERR) и результат выполнения задания (STDOUT) также записываются в хранилище HDInsight.

| Для данного задания... | Смотрите эти файлы в контейнере |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig, |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Дальнейшие действия

Если вам нужно загрузить модули Python, которые не поставляются по умолчанию, см. статью [How to deploy a Python module to Windows Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Как развернуть модуль Python в Windows Azure HDInsight).

Сведения о других способах использования Pig и Hive и дополнительную информацию об использовании MapReduce см. в следующих документах:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)
