---
title: "Средства Azure Data Lake для Visual Studio с песочницей Hortonworks | Документация Майкрософт"
description: "Узнайте, как использовать средства Azure Data Lake для Visual Studio с песочницей Hortonworks (работающей в локальной виртуальной машине). С помощью этих средств можно создавать и выполнять задания Hive и Pig в песочнице и просматривать выходные данные и журнал задания."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 599aeb1f38c804c2edf6590140e739c9705ab1ab
ms.lasthandoff: 03/07/2017


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Использование средств Azure Data Lake для Visual Studio с песочницей Hortonworks

Средства Azure Data Lake для Visual Studio включают в себя средства для работы с универсальными кластерами Hadoop, а также средства для работы с Azure Data Lake и HDInsight. В этом документе описываются действия, необходимые для использования средств Azure Data Lake с песочницей Hortonworks, работающей в локальной виртуальной машине.

Использование песочницы Hortonworks позволяет работать с Hadoop локально в среде разработки. Если вам необходимо развернуть решение в нужном масштабе после его разработки, можно перейти к кластеру HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Песочница Hortonworks, работающая в виртуальной машине в среде разработки. Описанные здесь сведения протестированы с использованием песочницы, работающей в Oracle VirtualBox, настроенном согласно инструкциям в документе [Начало работы в экосистеме Hadoop с песочницей Hadoop на виртуальной машине](hdinsight-hadoop-emulator-get-started.md) .

* Visual Studio 2013, 2015 или 2017, любой выпуск.

* [Пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/) версии 2.7.1 или более поздней.

* [Средства Azure Data Lake для Visual Studio.](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>Настройка паролей для песочницы

Убедитесь, что песочница Hortonworks работает, а затем следуйте инструкциям в статье [Начало работы в экосистеме Hadoop с песочницей Hadoop на виртуальной машине](hdinsight-hadoop-emulator-get-started.md#set-sandbox-passwords). Эти шаги используются для настройки пароля для учетной записи SSH `root` и учетной записи Ambari `admin`. Эти пароли используются при подключении к песочнице из Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Подключение инструментов к песочнице

1. Откройте Visual Studio и выберите **Просмотреть**, а затем — **Обозреватель серверов**.

2. В окне **Обозреватель серверов** щелкните правой кнопкой мыши запись **HDInsight**, а затем выберите **Connect to HDInsight Emulator** (Подключиться к эмулятору HDInsight).

    ![Connect to HDInsight Emulator](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. В диалоговом окне **Connect to HDInsight Emulator** (Подключение к эмулятору HDInsight) введите пароль, настроенный для Ambari.

    ![Ввод пароля Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Нажмите кнопку **Далее**, чтобы продолжить.

4. В поле **Пароль** введите пароль, настроенный для учетной записи `root`. Оставьте в остальных полях значения по умолчанию.

    ![Ввод пароля пользователя root](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Нажмите кнопку **Далее**, чтобы продолжить.

5. Подождите, пока не завершится проверка служб. В некоторых случаях проверка может завершиться ошибкой, и тогда появится запрос на обновление конфигурации. В случае ошибки проверки нажмите кнопку **Обновить** и подождите, пока не завершится настройка и проверка службы.

    ![Ошибки и кнопка "Обновить"](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > В процессе обновления используется Ambari, чтобы изменить конфигурацию песочницы Hortonworks до ожидаемых значений для средств Azure Data Lake для Visual Studio.

    После завершения проверки нажмите кнопку **Готово**, чтобы завершить настройку.

    ![Завершение подключения](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [!NOTE]
    > В зависимости от скорости среды разработки и объема памяти, выделенного для виртуальной машины, настройка и проверка служб может занять несколько минут.

После выполнения этих действий в обозревателе серверов в разделе HDInsight появится запись HDInsight local cluster (Локальный кластер HDInsight).

## <a name="write-a-hive-query"></a>Написание запроса Hive

Hive предоставляет язык запросов по типу SQL (HiveQL) для работы со структурированными данными. Чтобы узнать, как выполнять ad-hoc-запросы к локальному кластеру, сделайте следующее:

1. На вкладке **Обозреватель серверов** щелкните правой кнопкой мыши добавленную ранее запись для локального кластера, а затем выберите **Написать запрос Hive**.

    ![Написание запроса Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Откроется новое окно запроса, в котором можно быстро ввести и отправить запрос в локальный кластер.

2. В новом окне создания запроса введите следующую команду:

        select count(*) from sample_08;

    В верхней части окна запроса выберите конфигурацию для локального кластера, а затем нажмите кнопку **Отправить**. Оставьте в остальных полях (**Пакет** и "Имя сервера") значения по умолчанию.

    ![Окно запроса и кнопка "Отправить"](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Меню **Дополнительно** можно также открыть с помощью раскрывающегося меню рядом с кнопкой **Отправить**. В диалоговом окне дополнительных параметров можно указать дополнительные параметры при отправке задания.

    ![Расширенная отправка](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. После отправки запроса отобразится состояние задания. В нем содержатся сведения о ходе обработки задания с помощью Hadoop. В записи **Состояние задания** отображается состояние задания. Это состояние периодически обновляется. Его также можно обновить вручную, щелкнув значок обновления.

    ![Состояние задания](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Как только значение записи **Состояние задания** изменится на **Завершено**, отобразится направленный ациклический граф (DAG). На этой схеме показан путь выполнения, определенный с помощью Tez (механизм выполнения по умолчанию для Hive в локальном кластере).

    > [!NOTE]
    > Tez также используется по умолчанию при использовании кластеров HDInsight под управлением Linux. Он не используется по умолчанию в HDInsight под управлением Windows. Чтобы его использовать, необходимо добавить строку `set hive.execution.engine = tez;` в начало запроса Hive.

    Воспользуйтесь ссылкой **Job Output** (Выходные данные задания), чтобы просмотреть выходные данные. В этом случае это **823** — количество строк в таблице sample_08. Диагностические сведения о задании можно просмотреть, воспользовавшись ссылками **Журнал задания** и **Download YARN Log** (Скачать журнал YARN).

4. Кроме того, можно выполнять задания Hive интерактивно. Для этого нужно изменить значение поля **Пакет** на **Интерактивный**, а затем нажать кнопку **Выполнить**.

    ![Интерактивный запрос](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Интерактивный запрос передает журнал выходных данных, созданный во время обработки, в окно **HiveServer2 Output** (Выходные данные HiveServer2).

    > [!NOTE]
    > Это те же сведения, которые можно получить, перейдя по ссылке **Журнал задания** после завершения задания.

    ![HiveServer2 Output](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Создание проекта Hive

Вы также можете создать проект, который содержит несколько скриптов Hive. Проект полезен при наличии связанных скриптов, которые необходимо хранить вместе или поддерживать с помощью систем управления версиями.

1. Откройте Visual Studio, выберите **Файл**, **Создать**, а затем "Проект".

2. В списке проектов разверните список **Шаблоны**, **Azure Data Lake**, а затем выберите **HIVE (HDInsight)**. В списке шаблонов выберите **Hive Sample** (Пример Hive). Введите имя и расположение, а затем нажмите кнопку **ОК**.

    ![Шаблон Hive (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Проект **Hive Sample** (Пример Hive) содержит два скрипта: **WebLogAnalysis.hql** и **SensorDataAnalysis.hql**. Их можно отправить, нажав все ту же кнопку **Отправить** в верхней части окна.

## <a name="create-a-pig-project"></a>Создание проекта Pig

Тогда как Hive предоставляет язык по типу SQL для работы со структурированными данными, Pig работает, преобразуя данные. Pig предоставляет язык (Pig Latin), с помощью которого можно разрабатывать конвейер преобразований. Чтобы использовать Pig в локальном кластере, сделайте следующее:

1. Откройте Visual Studio, выберите **Файл**, **Создать**, а затем — **Проект**. В списке проектов разверните список **Шаблоны**, **Azure Data Lake**, а затем выберите **Pig (HDInsight)**. В списке шаблонов выберите **Pig Application** (Приложение Pig). Введите имя и расположение, а затем нажмите кнопку **ОК**.

    ![Проект Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Введите в качестве содержимого файла **script.pig**, созданного с помощью этого проекта, следующий текст.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Хотя в Pig и Hive используются разные языки, способ выполнения задания одинаковый для обоих языков (нужно нажать кнопку **Отправить**). Если выбрать раскрывающийся список рядом с кнопкой **Отправить**, отобразится диалоговое окно расширенной отправки для Pig.

    ![Расширенная отправка Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Состояние задания и выходные данные отображаются так же, как и для запроса Hive.

    ![Изображение завершенного задания Pig](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Просмотр заданий

Средства Azure Data Lake также позволяют с легкостью просмотреть сведения о заданиях, выполненных на Hadoop. Чтобы увидеть задания, выполненные в локальном кластере, сделайте следующее:

1. В окне **Обозреватель серверов** щелкните правой кнопкой мыши локальный кластер, а затем выберите **Просмотреть задания**. Отобразится список заданий, переданных в кластер.

    ![Просмотр заданий](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Выберите в этом списке задание, чтобы просмотреть сведения о нем.

    ![Выбор задания](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Отображаемые сведения похожи на сведения, отображаемые после выполнения запроса Pig и Hive, дополненные ссылками для просмотра выходных данных и сведений журнала.

3. Кроме того, на этом этапе можно изменить и повторно отправить задания.

## <a name="view-hive-databases"></a>Просмотр баз данных Hive

1. В окне **Обозреватель серверов** разверните запись **HDInsight local cluster** (Локальный кластер HDInsight), а затем — **Hive Databases** (Базы данных Hive). В локальном кластере отобразятся базы данных **по умолчанию** и **xademo**. Если развернуть базу данных, отобразятся все таблицы, содержащиеся в ней.

    ![Развернутые базы данных](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Если развернуть таблицу, отобразятся все столбцы этой таблицы. Чтобы быстро просмотреть данные, можно щелкнуть таблицу правой кнопкой мыши и выбрать **Просмотреть первые 100 строк**.

    ![Просмотр баз данных Hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Свойства базы данных и таблицы

Возможно, вы уже заметили, что можно просмотреть **свойства** базы данных или таблицы. После нажатия **Свойства** отобразятся сведения для выбранного элемента в окне свойств.

![Свойства](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Создание таблицы

Чтобы создать таблицу, щелкните базу данных правой кнопкой мыши и выберите **Создать таблицу**.

![Создать таблицу](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Затем можно создать таблицу с помощью формы. Вы увидите необработанный скрипт HiveQL, который будет использоваться для создания таблицы в нижней части этой страницы.

![Форма создания таблицы](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Руководство по началу работы с Hadoop)

