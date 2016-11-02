<properties
   pageTitle="Использование Hadoop Pig с SSH в кластере HDInsight | Microsoft Azure"
   description="Узнайте, как подключиться к кластеру Hadoop под управлением Linux с помощью SSH, а затем использовать команду Pig для выполнения операторов Pig Latin в интерактивном режиме или в виде пакетного задания."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>


#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-(ssh)"></a>Выполнение заданий Pig в кластере под управлением Linux с помощью команды Pig (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведены пошаговые инструкции по подключению к кластеру Azure HDInsight на платформе Linux с использованием протокола Secure Shell (SSH), а также использованию команды Pig для выполнения операторов Pig Latin в интерактивном режиме или в качестве пакетного задания.

Язык программирования Pig Latin позволяет описывать преобразования, применяемые к входным данным для получения требуемых выходных данных.

> [AZURE.NOTE] Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, ознакомьтесь со статьей [Советы по использованию HDInsight на платформе Linux](hdinsight-hadoop-linux-information.md).

##<a name="<a-id="prereq"></a>prerequisites"></a><a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight на основе Linux (Hadoop в HDInsight).

* Клиент SSH. Клиент SSH должен входить в состав Linux, Unix и Mac OS. Пользователи Windows должны загрузить отдельный клиент, например [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a name="<a-id="ssh"></a>connect-with-ssh"></a><a id="ssh"></a>Подключение по SSH

Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя — это имя, присвоенное кластеру, с суффиксом **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для аутентификации SSH** , возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**для аутентификации SSH был задан пароль** , при появлении запроса необходимо будет ввести пароль.

Дополнительную информацию об использовании SSH с HDInsight см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-(windows-based-clients)"></a>PuTTY (клиенты под управлением Windows)

Windows не предоставляет встроенный клиент SSH. Рекомендуется использовать **PuTTY**, который можно скачать по адресу: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Дополнительные сведения об использовании PuTTY см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a name="<a-id="pig"></a>use-the-pig-command"></a><a id="pig"></a>Использование команды Pig

2. После установления соединения запустите интерфейс командной строки Pig с помощью следующей команды.

        pig

    Через некоторое время отобразится командная строка `grunt>` .

3. Введите следующую инструкцию.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Эта команда загружает содержимое файла sample.log в LOGS. Вы можете просмотреть содержимое файла следующим образом.

        DUMP LOGS;

4. Затем преобразуйте данные следующим образом, применив регулярное выражение для извлечения из каждой записи только информации об уровне ведения журнала.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Вы можете использовать **DUMP** , чтобы просмотреть данные после преобразования. В этом случае используйте `DUMP LEVELS;`.

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

6. Можно также сохранить результаты преобразования с помощью оператора `STORE` . Например, следующий оператор сохраняет `RESULT` в каталог **/example/data/pigout** в используемом по умолчанию контейнере хранилища для кластера.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Данные хранятся в указанном каталоге в файлах с именем **part-№№№№№**. Если каталог уже существует, появится сообщение об ошибке.

7. Чтобы выйти из командной строки grunt, введите следующий оператор.

        QUIT;

###<a name="pig-latin-batch-files"></a>Пакетные файлы Pig Latin

Вы также можете использовать команду Pig для выполнения кода Pig Latin, содержащегося в файле.

3. После выхода из командной строки grunt используйте следующую команду, чтобы связать STDIN с файлом с именем **pigbatch.pig**. Этот файл будет создан в домашнем каталоге учетной записи, которая использовалась для входа в сеанс SSH.

        cat > ~/pigbatch.pig

4. Введите или вставьте следующие строки, а когда все будет готово, используйте клавиши CTRL+D.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Используйте следующую команду, чтобы запустить файл **pigbatch.pig** с помощью команды Pig.

        pig ~/pigbatch.pig

    После завершения пакетного задания вы должны увидеть следующий результат, который должен быть таким же, как при использовании `DUMP RESULT;` в предыдущих шагах.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a name="<a-id="summary"></a>summary"></a><a id="summary"></a>Сводка

Как вы видите, команда Pig позволяет интерактивно выполнять операции MapReduce с помощью Pig Latin, а также выполнять инструкции, хранимые в пакетном файле.

##<a name="<a-id="nextsteps"></a>next-steps"></a><a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)



<!--HONumber=Oct16_HO2-->


