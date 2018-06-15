---
title: Использование Hadoop Pig и удаленного рабочего стола в HDInsight — Azure | Документы Майкрософт
description: Узнайте, использовать команду Pig для выполнения операторов Pig Latin через подключение к удаленному рабочему столу, установленное с кластером Hadoop под управлением Windows в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 700569684888428032997dd81c10023c6fd5a7d8
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823311"
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Выполнение заданий Pig через подключение к удаленному рабочему столу
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Этот документ содержит пошаговое руководство по использованию команды Pig для выполнения операторов Pig Latin через подключение к удаленному рабочему столу, установленное с кластером HDInsight под управлением Windows. Pig Latin позволяет создавать приложения MapReduce, описывая преобразования данных, а не функции сопоставления и приведения.

> [!IMPORTANT]
> Удаленный рабочий стол доступен только в кластерах HDInsight под управлением операционной системы Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> При использовании HDInsight 3.4 или более поздней версии см. сведения о выполнении интерактивных заданий Pig непосредственно в кластере из командной строки: [Use Pig with HDInsight and SSH](apache-hadoop-use-pig-ssh.md) (Использование Pig в HDInsight с помощью SSH).

## <a id="prereq"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight на платформе Windows (Hadoop в HDInsight).
* Клиентский компьютер под управлением Windows 10, Windows 8 или Windows 7.

## <a id="connect"></a>Подключение к удаленному рабочему столу
Запустите протокол удаленного рабочего стола для кластера HDInsight, а затем выполните подключение, следуя инструкциям раздела [Подключение к кластерам HDInsight с использованием RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Использование команды Pig
1. Установив подключение к удаленному рабочему столу, запустите **командную строку Hadoop** с помощью значка на рабочем столе.
2. Для выполнения команды Pig используйте следующее:

        %pig_home%\bin\pig

    Откроется командная строка `grunt>` .
3. Введите следующий оператор:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Эта команда загружает содержимое файла sample.log в файл LOGS. Вы можете просмотреть содержимое файла с помощью следующей команды:

        DUMP LOGS;
4. Преобразуйте данные, применив регулярное выражение для извлечения из каждой записи только информации об уровне ведения журнала:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Вы можете использовать **DUMP** , чтобы просмотреть данные после преобразования. В этом случае — `DUMP LEVELS;`.
5. Продолжайте применение преобразований с помощью следующих инструкций. Используйте `DUMP` для просмотра результатов преобразования после каждого шага.

    <table>
    <tr>
    <th>Инструкция</th><th>Действие</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Удаляет строки, содержащие значение NULL для уровня ведения журнала и сохраняет результаты в FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Группирует строки по уровню ведения журнала и сохраняет результаты в GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Создает новый набор данных, который содержит каждое уникальное значение уровня ведения журнала и количество его вхождений. Он сохраняется в FREQUENCIES.</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Упорядочивает уровни ведения журнала по количеству (по убыванию) и сохраняет данные в RESULT.</td>
    </tr>
</table>
6. Можно также сохранить результаты преобразования с помощью оператора `STORE`. Например, следующий оператор сохраняет `RESULT` в каталог **/example/data/pigout** в используемом по умолчанию контейнере хранилища для кластера:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Данные хранятся в указанном каталоге в файлах с именем **part-№№№№№**. Если каталог уже существует, появится сообщение об ошибке.
   >
   >
7. Чтобы выйти из командной строки grunt, введите следующий оператор.

        QUIT;

### <a name="pig-latin-batch-files"></a>Пакетные файлы Pig Latin
Вы также можете использовать команду Pig для выполнения кода Pig Latin, содержащегося в файле.

1. После выхода из командной строки grunt откройте **Блокнот** и создайте новый файл с именем **pigbatch.pig** в каталоге **%PIG_HOME%**.
2. Введите или вставьте следующие строки в файл **pigbatch.pig** , а затем сохраните его:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Используйте следующую команду, чтобы запустить файл **pigbatch.pig** с помощью команды Pig.

        pig %PIG_HOME%\pigbatch.pig

    После завершения пакетного задания вы должны увидеть следующий результат, который должен быть таким же, как при использовании `DUMP RESULT;` в предыдущих шагах.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Сводка
Как вы видите, команда Pig позволяет интерактивно выполнять операции MapReduce или задания Pig Latin, хранимые в пакетном файле.

## <a id="nextsteps"></a>Дальнейшие действия
Общая информация о Pig в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
