---
title: Подключение к Azure HDInsight с помощью средств Data Lake для Visual Studio | Документация Майкрософт
description: Узнайте, как установить и использовать средства Data Lake для Visual Studio, чтобы подключиться к кластерам Hadoop в Azure HDInsight, а затем выполнить запросы Hive.
keywords: средства hadoop, запрос hive, visual studio, visual studio hadoop
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 1707a681315fbb53f21df95664188463968bb855
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202481"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio

В этой статье описано, как использовать средства Data Lake для Visual Studio (они также называются средства Azure Data Lake и Stream Analytics для Visual Studio), чтобы подключиться к кластерам Hadoop в [Azure HDInsight](../hdinsight-hadoop-introduction.md) и отправлять запросы Hive. 

Дополнительные сведения об использовании HDInsight см. в статьях [Введение в экосистему Hadoop в Azure HDInsight](../hdinsight-hadoop-introduction.md) и [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md). 

Дополнительные сведения о подключении к кластеру Storm см. в статье [Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Средства Data Lake для Visual Studio можно использовать для доступа к Azure Data Lake Analytics и HDInsight. Дополнительные сведения см. в статье [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством и использования средств Data Lake для Visual Studio потребуются следующие компоненты:

* Кластер Azure HDInsight. Сведения о создании этого кластера см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md). Чтобы выполнять интерактивные запросы Hive, вам потребуется кластер с [интерактивными запросами HDInsight](../interactive-query/apache-interactive-query-get-started.md).
* Установите на компьютере Visual Studio 2017, 2015 или 2013.
    
    > [!NOTE]
    > Сейчас доступна только английская версия средств Data Lake для Visual Studio.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Установка или обновление средств Data Lake для Visual Studio

### <a name="install-data-lake-tools"></a>Установка средств Data Lake

Инструменты Data Lake устанавливаются по умолчанию для Visual Studio 2017. Для более ранних версий Visual Studio вы можете установить средства Data Lake с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx). Выберите версию средств Data Lake, которая соответствует вашей версии Visual Studio. 

### <a name="install-visual-studio"></a>Установка Visual Studio

Если на вашем компьютере не установлена программа Visual Studio, вы можете установить последние версии Visual Studio Community и пакета Azure SDK с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).

![Снимок экрана средств Data Lake для установщика веб-платформы Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Использование установщика веб-платформы для установки средств Data Lake для Visual Studio")

### <a name="update-the-tools"></a>Обновление средств

1. Откройте Visual Studio.
2. Откройте меню **Средства** и выберите пункт **Расширения и обновления**.
3. Разверните узел **Обновления**, а затем выберите **средства Azure Data Lake и Stream Analytics**, если они установлены.

> [!NOTE]
>
> Чтобы подключиться к кластерам Interactive Query и выполнять интерактивные запросы Hive, можно использовать средства Data Lake только версии 2.3.0.0 или более поздней.

## <a name="connect-to-azure-subscriptions"></a>Подключение к подпискам Azure
Средства Data Lake для Visual Studio позволяют подключаться к кластерам HDInsight, выполнять некоторые базовые операции управления и запросы Hive.

> [!NOTE]
> Дополнительные сведения о подключении к универсальному кластеру Hadoop см. в записи блога [How to write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Создание и отправка запросов Hive с помощью Visual Studio).
> 
> 

Чтобы подключиться к подписке Azure, сделайте следующее:

1. Откройте Visual Studio.
2. В меню **Представление** выберите **обозреватель сервера**.
3. В обозревателе сервера разверните **Azure**, а затем — **HDInsight**.
   
   > [!NOTE]
   > Должно открыться окно **Список задач HDInsight**. Если вы не видите это окно, в меню **Представление** выберите **Other Windows** (Другие окна), а затем — **окно списка задач HDInsight**.  
   > 
   > 
4. Введите учетные данные подписки Azure и нажмите кнопку **Войти**. Аутентификация необходима, только если подключение к подписке Azure из Visual Studio на этом компьютере ранее не выполнялось.
5. В обозревателе сервера отобразится список имеющихся кластеров HDInsight. Если кластеров нет в наличии, их можно создать с помощью портала Azure, оболочки Azure PowerShell или пакета SDK для HDInsight. Дополнительные сведения см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Снимок экрана со списком кластеров для средств Data Lake для Visual Studio в обозревателе сервера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Список кластеров для средств Data Lake для Visual Studio в обозревателе сервера")
6. Разверните кластер HDInsight. Отобразятся **базы данных Hive**, учетная запись хранения по умолчанию, связанные учетные записи хранения и **журнал службы Hadoop**. Развертывание объектов можно продолжить.

После подключения к подписке Azure можно будет выполнить следующие задачи.

Чтобы подключиться к порталу Azure из Visual Studio, сделайте следующее:

1. В обозревателе сервера последовательно выберите пункты **Azure** > **HDInsight**.
2. Щелкните правой кнопкой мыши кластер HDInsight, а затем выберите **Manage Cluster in Azure portal** (Управление кластером на портале Azure).

Для отправки вопросов и отзывов из Visual Studio:

1. В меню **Инструменты** выберите **HDInsight**.
2. Чтобы задать вопрос, выберите **Форум MSDN**. Чтобы отправить отзыв, выберите **Отправить отзыв**.

## <a name="explore-linked-resources"></a>Анализ связанных ресурсов
В обозревателе сервера отобразятся учетная запись хранения по умолчанию и все связанные учетные записи хранения. Если развернуть учетную запись хранения по умолчанию, отобразятся контейнеры в учетной записи хранения. Учетная запись хранения по умолчанию и контейнер по умолчанию отмечены. Можно просмотреть содержимое любого контейнера, щелкнув его правой кнопкой мыши.

![Снимок экрана со списком связанных ресурсов для средств Data Lake для Visual Studio в обозревателе сервера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Список связанных ресурсов")

Когда контейнер откроется, с помощью следующих кнопок вы сможете передавать, удалять и скачивать большие двоичные объекты.

![Снимок экрана операций с большими двоичными объектами для средств Data Lake для Visual Studio в обозревателе сервера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Обновление, удаление и загрузка больших двоичных объектов в обозревателе сервера")

## <a name="run-interactive-hive-queries"></a>Выполнение интерактивных запросов Hive
[Apache Hive](http://hive.apache.org) — это инфраструктура хранилища данных, встроенная в Hadoop. Hive используется, чтобы обобщать, запрашивать и анализировать данные. Средства Data Lake для Visual Studio поддерживают выполнение запросов Hive из Visual Studio. Дополнительные сведения о Hive см. в статье [Использование Hive и HiveQL с Hadoop в HDInsight](hdinsight-use-hive.md).

[Интерактивный запрос](../interactive-query/apache-interactive-query-get-started.md) использует [Hive в LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) в Apache Hive версии 2.1. Он обеспечивает интерактивность для сложных запросов в хранилище данных в больших хранимых наборах данных. Запросы Hive в кластере интерактивных запросов выполняются гораздо быстрее по сравнению с традиционными пакетными заданиями Hive. Дополнительные сведения см. в разделе [Выполнение пакетных заданий Hive](#run-hive-batch-jobs).

> [!NOTE]
>
> Выполнение интерактивных запросов Hive поддерживается только при подключении к кластеру [интерактивных запросов HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Вы также можете использовать средства Data Lake для Visual Studio, чтобы увидеть задание Hive изнутри. Средства Data Lake для Visual Studio собирают и обрабатывают журналы YARN определенных заданий Hive.

### <a name="view-hivesampletable"></a>Просмотр таблицы **hivesampletable**
Все кластеры HDInsight содержат пример таблицы Hive по умолчанию, которая называется hivesampletable. Эта таблица Hive определяет способ отображения списка таблиц Hive, просмотра схемы таблицы и отображения списка строк в таблице Hive.

Чтобы получить список таблиц Hive и просмотреть схемы этой таблицы, сделайте следующее:

1. Чтобы просмотреть схему в таблице, в **обозревателе сервера** последовательно выберите пункты **Azure** > **HDInsight**. Выберите кластер, а затем — **Базы данных Hive** > **По умолчанию** > **hivesampletable**.
2. Щелкните **hivesampletable** правой кнопкой мыши, а затем выберите **Просмотреть первые 100 строк**. Это эквивалентно выполнению следующего запроса Hive с помощью драйвера Hive ODBC:
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   Подсчет строк можно настроить.
   
   ![Снимок экрана запроса схемы Hive в HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Результаты запроса Hive")

### <a name="create-hive-tables"></a>Создание таблиц Hive
Таблицу Hive можно создать с помощью графического интерфейса пользователя (GUI) или запросов Hive. Дополнительные сведения об использовании запросов Hive см. в разделе [Выполнение запросов Hive](#run.queries).

Чтобы создать таблицу Hive, сделайте следующее:

1. В **обозревателе сервера** последовательно выберите пункты **Azure** > **Кластеры HDInsight**. Выберите свой кластер HDInsight, а затем — **Базы данных Hive**.
2. Щелкните правой кнопкой мыши **по умолчанию**, а затем выберите **Создать таблицу**.
3. Настройка таблицы.  
4. Выберите **Создать таблицу**, чтобы отправить задание для создания таблицы Hive.
   
    ![Снимок экрана окна создания таблицы в средствах Visual Studio для HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Создание таблицы Hive")

### <a name="run.queries"></a>Проверка и выполнение запросов Hive
Есть два способа создания и выполнения запросов Hive.

* Создание ad-hoc-запросов
* Создание приложения Hive

Для создания, проверки и выполнения запросов ad-hoc сделайте следующее:

1. В **обозревателе сервера** последовательно выберите пункты **Azure** > **Кластеры HDInsight**.
2. Правой кнопкой мыши щелкните кластер, в котором вы хотите выполнить запрос, и выберите **Write a Hive Query** (Написать запрос Hive).  
3. Введите запросы Hive. 

    Редактор Hive поддерживает технологию IntelliSense. Средства Data Lake для Visual Studio поддерживают загрузку удаленных метаданных при редактировании скрипта Hive. Например, если ввести **SELECT * FROM**, IntelliSense отобразит все предлагаемые имена таблиц. Если указано имя таблицы, IntelliSense выведет список имен столбцов. Эти инструменты поддерживают почти все инструкции, подзапросы и встроенные определяемые пользователем функции Hive DML.
   
    ![Снимок экрана примера 1 IntelliSense в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![Снимок экрана примера 2 IntelliSense в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > IntelliSense предлагает только метаданные кластеров, выбранных на панели инструментов HDInsight.
   > 
   
4. (Необязательно.) Щелкните **Validate Script** (Проверить скрипт), чтобы проверить наличие синтаксических ошибок в скрипте.
   
    ![Снимок экрана локальной проверки в средствах Data Lake для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Проверка сценария")
5. Выберите **Отправить** или выполните **расширенную отправку**. С помощью расширенной отправки можно настроить **имя задания**, **аргументы**, **дополнительные конфигурации** и **каталог состояния** скрипта.
   
    ![Снимок экрана запроса Hive в HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Отправка запросов")
   
    После отправки задания отобразится окно **сводки по заданию Hive**.
   
    ![Снимок экрана сводки запроса Hive в HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Сводка по заданию Hive")
6. Используйте кнопку **Обновить**, чтобы обновлять состояние задания, пока оно не сменится на **Завершено**.
7. Перейдите по ссылкам внизу, чтобы просмотреть **запрос задания**, **выходные данные задания**, **журнал задания** или **журнал YARN**.

Для создания и запуска решения Hive сделайте следующее:

1. В меню **Файл** выберите **Создать**, а затем — **Проект**.
2. В левой области выберите **HDInsight**. В средней области выберите **Hive Application** (Приложение Hive). Введите свойства, а затем нажмите кнопку **ОК**.
   
    ![Снимок экрана нового проекта Hive в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Создание приложений Hive из Visual Studio")
3. В **обозревателе решений** дважды щелкните файл **Script.hql**, чтобы открыть его.
4. Чтобы проверить скрипт Hive, нажмите кнопку **Validate Script** (Проверить скрипт). Можно также щелкнуть скрипт правой кнопкой мыши в редакторе Hive, а затем выбрать команду **проверки скрипта** в контекстном меню.

### <a name="view-hive-jobs"></a>Просмотр заданий Hive
Для заданий Hive можно просмотреть запросы задания, выходные данные задания, журнал задания и журналы YARN. Дополнительные сведения см. на снимке экрана выше.

Самая последняя версия средств позволяет увидеть, что находится внутри заданий Hive, собирая и отображая журналы YARN. Журнал YARN может помочь исследовать проблемы производительности. Дополнительные сведения о том, как HDInsight собирает журналы YARN, см. в статье [Доступ к журналам приложений YARN в HDInsight под управлением Windows](../hdinsight-hadoop-access-yarn-app-logs.md).

Для просмотра заданий Hive сделайте следующее:

1. В **обозревателе сервера** разверните **Azure**, а затем — **HDInsight**.
2. Щелкните кластер HDInsight правой кнопкой мыши, а затем выберите пункт **Просмотреть задания**. Появится список заданий Hive, выполняющихся в кластере.  
3. Выберите задание. В окне **сводки заданий Hive** выберите один из пунктов ниже.
    - **Запрос задания**
    - **Выходные данные задания**
    - **Журнал задания**  
    - **Журнал YARN**
   
    ![Снимок экрана окна просмотра заданий Hive в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Просмотр заданий Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Быстрый путь выполнения Hive с помощью HiveServer2
> [!NOTE]
> Эта функция работает только в кластере HDInsight версии 3.2 или более поздней.
 
В средствах Data Lake для Visual Studio для отправки заданий Hive использовался интерфейс [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (также известен как Templeton). В этом случае возврат сведений о задании и об ошибках занимает много времени.

Чтобы решить эту проблему производительности, средства Data Lake для Visual Studio могут обходить протоколы RDP и SSH и выполнять задания Hive непосредственно в кластере через HiveServer2.

Кроме повышения производительности с помощью этого метода вы также можете просматривать диаграммы выполнения заданий Hive в Apache Tez, а также сведения о задаче.

При использовании кластера HDInsight 3.2 или более поздней версии отобразится кнопка **Выполнить с помощью HiveServer2** (Выполнить через HiveServer2):

![Снимок экрана средств Data Lake для Visual Studio: кнопка выполнения через HiveServer2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Выполнение запросов Hive с помощью HiveServer2")

Вы также можете просмотреть журналы, отображаемые в режиме реального времени, а также графы задания при выполнении запроса Hive в Tez.

![Снимок экрана средств Data Lake для Visual Studio: быстрый путь выполнения Hive с помощью HiveServer2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Просмотр графов заданий")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Выполнение запросов через HiveServer2 и отправка запросов через WebHCat

Выполнение запросов через HiveServer2 имеет множество преимуществ в отношении производительности, однако есть и ряд ограничений. Некоторые ограничения не позволяют использовать эту функцию в рабочей среде. 

В таблице ниже приводятся отличия между выполнением запросов через HiveServer2 и их отправкой через WebHCat.

|  | Выполнение с помощью HiveServer2 | Отправка через WebHCat |
| --- | --- | --- |
| Выполнение запросов |Устраняет временные затраты на сервере WebHCat (который запускает задание MapReduce с именем TempletonControllerJob). |Так как запрос выполняется через WebHCat, WebHCat запускает задание MapReduce, что приводит к дополнительной задержке. |
| Потоковая передача журналов |Почти в реальном времени. |Журналы выполнения заданий доступны, только если задание завершено. |
| Просмотр журнала заданий |Если запрос выполняется через HiveServer2, его журнал заданий (выходные данные заданий) не сохраняется. Приложение можно просмотреть в пользовательском интерфейсе YARN с ограниченными сведениями. |Если запрос выполняется через WebHCat, его журнал заданий (выходные данные заданий) сохраняется. Журнал заданий можно просмотреть с помощью Visual Studio, пакета SDK HDInsight или PowerShell. |
| Закрытие окна |Выполнение через HiveServer2 является *синхронной* операцией, поэтому окна должны оставаться открытыми. Если закрыть окна, выполнение запроса будет отменено. |Отправка через WebHCat является *асинхронной* операцией, поэтому вы можете отправить запрос через WebHCat и закрыть Visual Studio. Вы можете вернуться и просмотреть результаты в любое время. |

### <a name="tez-hive-job-performance-graph"></a>Диаграмма выполнения задания Tez Hive
В средствах Data Lake для Visual Studio вы можете просмотреть графы производительности для заданий Hive, которые запускает подсистема выполнения Tez. Сведения о том, как включить Tez, см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](hdinsight-use-hive.md). 

После отправки в Visual Studio задания Hive Visual Studio отобразит граф по завершении задания. Возможно, вам потребуется нажать кнопку **Обновить**, чтобы получить последние сведения о состоянии задания.

> [!NOTE]
> Эта функция доступна только в кластере HDInsight версии 3.2.4.593 или более поздней, и ее можно использовать только для завершенных заданий. Чтобы использовать эту функцию, вам также нужно отправить задание через WebHCat. При выполнении запроса через HiveServer2 появится следующее изображение: 
> 
> ![Снимок экрана графы производительности Tez для Hadoop Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Состояние задания")

Чтобы упростить понимание запросов Hive, в этот выпуск добавлена функция просмотра операторов Hive. Чтобы увидеть все операторы вершины, дважды щелкните вершину графа задания. Можно выбрать также определенный оператор, чтобы увидеть о нем дополнительные сведения.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Представление выполнения задачи для заданий Hive в Tez
Представление выполнения задачи для заданий Hive в Tez позволяет просматривать подробные и структурированные сведения о заданиях Hive. Но можно также получить и дополнительные сведения. При появлении проблем с производительностью представление можно использовать для получения дополнительных сведений о проблеме. Например, вы можете получить сведения о работе всех задач и подробную информацию о каждой задаче (чтение и запись данных, время запланированного запуска и завершения и т. д.). Эти наглядные сведения помогут вам изменить конфигурацию задания или архитектуру системы.

![Снимок экрана средств Data Lake для Visual Studio: представление выполнения заданий](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Представление выполнения заданий")

## <a name="run-hive-batch-jobs"></a>Выполнение пакетных заданий Hive
Тестирование скрипта Hive в кластере HDInsight может длиться очень долго. Исключением является кластер интерактивных запросов. Этот процесс может занять от нескольких минут. Средства Data Lake для Visual Studio могут проверить скрипт Hive локально без подключения к кластеру в оперативном режиме. Дополнительные сведения о выполнении интерактивных запросов см. в разделе [Выполнение интерактивных запросов Hive](#run-interactive-hive-queries).

Средства Data Lake для Visual Studio также позволяют пользователям видеть, что находится внутри задания Hive, собирая и отображая журналы YARN определенных заданий Hive.

Дополнительные сведения о выполнении пакетных заданий Hive см. в разделе [Выполнение интерактивных запросов Hive](#run-interactive-hive-queries). Информация в этом разделе относится к пакетным заданиям Hive, которые выполняются дольше.

## <a name="run-pig-scripts"></a>Запуск сценариев Pig
Средства Data Lake для Visual Studio поддерживают создание и отправку скриптов Pig в кластеры HDInsight. Сначала создайте проект Pig из шаблона. Затем отправьте скрипт в кластеры HDInsight.

## <a name="feedback-and-known-issues"></a>Отзывы и известные проблемы
* Сейчас результаты HiveServer2 отображаются как обычный текст, что не совсем удобно. Корпорация Майкрософт работает над устранением этой проблемы.
* Проблема, при которой результаты, начинающиеся со значений Null, не отображались, исправлена. Если эта проблема мешает вам работать, обратитесь в нашу службу поддержки.
* Скрипт HQL, созданный Visual Studio, кодируется в зависимости от параметров региона пользователя. Скрипт выполняется неправильно, если вы загружаете его в кластер как двоичный файл.

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как подключаться к кластерам HDInsight из Visual Studio, используя пакет средств Data Lake для Visual Studio, и выполнять запросы Hive. Дополнительные сведения вы найдете в следующих статьях:

* [Использование Hive и HiveQL с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Отправка заданий Hadoop в HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Анализ данных Twitter с помощью Hive в HDInsight](../hdinsight-analyze-twitter-data.md)

