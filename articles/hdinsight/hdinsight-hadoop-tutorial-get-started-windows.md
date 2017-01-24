---
title: "Руководство по началу работы с Hadoop в Windows | Документация Майкрософт"
description: "Начало работы с Hadoop в HDInsight. Узнайте, как создавать кластеры Hadoop в Windows, выполнять запросы Hive с данными и анализировать выходные данные в Excel."
keywords: "руководство по hadoop, hadoop в windows, кластер hadoop, изучение hadoop, запрос hive"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 51a83082-f6e7-4ed5-8d3e-949f302c4d04
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/07/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: 176c06bd1d8117fda70001762f86f1a98829f78b


---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight-on-windows"></a>Руководство по Hadoop: приступая к работе с Hadoop в HDInsight на платформе Windows
> [!div class="op_single_selector"]
> * [На основе Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
> * [На основе Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
> 
> 

Чтобы вы смогли понять, что такое Hadoop в Windows, и начать работу с HDInsight, в этом учебнике показано, как выполнять запрос Hive на неструктурированных данных в кластере Hadoop, а затем анализировать результаты в Microsoft Excel.

> [!NOTE]
> Информация, приведенная в этом документе, относится только к кластерам HDInsight под управлением Windows. Сведения о кластерах под управлением Linux см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
> 
> 

Предположим, у вас есть большой набор неструктурированных данных, и необходимо выполнить запрос Hive для извлечения из них значимой информации. Именно этим мы и собираемся заняться в этом руководстве. Вот как это можно осуществить:

   ![Руководство по Hadoop: создание учетной записи; создание кластера Hadoop; отправка запроса Hive; анализ данных в Excel.][image-hdi-getstarted-flow]

Просмотрите демонстрационный видеоролик из этого учебника, чтобы узнать о кластерах Hadoop для HDInsight:

![Видео первого учебника Hadoop: отправка запроса Hive в кластере Hadoop и анализ результатов в Excel.][img-hdi-getstarted-video]

**[Просмотр руководства по Hadoop для HDInsight на YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**

Наряду с выпуском общедоступной версии Azure HDInsight корпорация Майкрософт выпустила также эмулятор HDInsight для Azure, ранее известный как *Microsoft HDInsight Developer Preview*. Этот продукт предназначен для сценариев разработки и поэтому поддерживает только развертывания на одном узле. Дополнительные сведения об использовании эмулятора HDInsight см. в статье [Начало работы в экосистеме Hadoop с песочницей Hadoop на виртуальной машине][hdinsight-emulator].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Рабочая станция** с Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hadoop-clusters"></a>Создание кластеров Hadoop
При создании кластера происходит создание вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Этот раздел поможет вам создать кластер HDInsight версии 3.2. Вы также можете создать кластеры Hadoop для других версий. Инструкции см. в статье [Создание кластеров Hadoop под управлением Windows в HDInsight][hdinsight-provision]. Дополнительную информацию о версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight](hdinsight-component-versioning.md).

**Создание кластера Hadoop**

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Создать**, **Данные+аналитика**, а затем — **HDInsight**. На портале откроется колонка **Новый кластер HDInsight** .
   
    ![Создание кластера на портале Azure](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.1.png "Создание кластера на портале Azure")
3. Введите или выберите следующие значения:
   
    ![Введите имя и тип кластера](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.2.png "Введите имя и тип кластера")
   
   | Имя поля | Значение |
   | --- | --- |
   | Имя кластера, |Уникальное имя для определения кластера |
   | Тип кластера |Для работы с этим руководством выберите **Hadoop** . |
   | Операционная система кластера |Для работы с этим руководством выберите **Windows Server 2012 R2 Datacenter** . |
   | Версия HDInsight |Для работы с этим руководством установите последнюю версию. |
   | Подписки |Выберите подписку Azure, которая будет использоваться для кластера. |
   | Группа ресурсов |Выберите существующую группу ресурсов Azure или создайте новую. Базовый кластер HDInsight содержит кластер и соответствующую учетную запись хранения, используемую по умолчанию.  Их можно объединить в группу ресурсов для простоты управления. |
   | Учетные данные |Введите имя пользователя для входа в кластер и пароль. Кластер на платформе Windows может включать две учетные записи.  Пользователь кластера (или пользователь HTTP) отвечает за управление кластером и отправку заданий.  Дополнительно можно создать учетную запись пользователя удаленного рабочего стола (RDP) для удаленного подключения к кластеру. Если вы решили использовать удаленный рабочий стол, следует создать учетную запись пользователя RDP. |
   | Источник данных |Чтобы создать новую учетную запись хранения, используемую по умолчанию, щелкните «Создать». В качестве имени контейнера, используемого по умолчанию, используйте имя кластера. В каждом кластере HDinsight есть контейнер больших двоичных объектов, используемый по умолчанию. Он расположен в учетной записи хранения Azure.  Расположение учетной записи хранения Azure, используемой по умолчанию, определяет расположение кластера HDInsight. |
   | Ценовые категории узлов |Для этого руководства используйте один или два рабочих узла (один из них является рабочим узлом по умолчанию) и ценовую категорию головного узла. |
   | Дополнительная настройка |Пропустите этот раздел. |
4. В колонке **Новый кластер HDInsight** выберите параметр **Закрепить на начальной панели** и нажмите **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка. Значок указывает, что выполняется создание кластера. После завершения создания вместо него будет отображаться значок HDInsight.
   
   | Во время создания | Создание завершено |
   | --- | --- |
   | ![Индикатор подготовки на начальной панели](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioning.png) |![Плитка создания кластера](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioned.png) |
   
   > [!NOTE]
   > Обычно создание кластера занимает около 15 минут. Вы можете отслеживать процесс создания с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.
   > 
   > 
5. После завершения создания щелкните элемент кластера на начальной панели, чтобы открыть колонку кластера.

## <a name="run-a-hive-query-from-the-portal"></a>Выполнение запроса Hive на портале
Теперь, когда вы создали кластер HDInsight, мы выполним задание Hive для запроса примера таблицы Hive. Мы используем таблицу *hivesampletable*, которая поставляется с кластерами HDInsight. Эта таблица содержит данные о производителях мобильных устройств, платформах и моделях. Запрос Hive в этой таблице извлекает данные для мобильных устройств определенного производителя.

> [!NOTE]
> Инструменты HDInsight для Visual Studio поставляются с пакетом SDK для Azure для .NET версии 2.5 или более поздней. Используя инструменты Visual Studio, вы можете подключить кластер HDInsight, создать таблицы Hive и выполнить Hive-запросы. Дополнительные сведения см. в статье [Приступая к работе с инструментами Azure Data Lake (в HDInsight) для Visual Studio для выполнения запроса Hive][1].
> 
> 

**Выполнение задания Hive с панели мониторинга кластера**

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Просмотреть все**, а затем выберите **Кластеры HDInsight**. После этого откроется список кластеров, включая только что созданный кластер (см. предыдущий раздел).
3. Щелкните имя кластера, в котором необходимо запустить задание Hive, а затем нажмите кнопку **Панель мониторинга** в верхней части колонки.
4. В новой вкладке браузера откроется веб-страница. Введите учетную запись и пароль пользователя Hadoop. Имя по умолчанию — **admin**; пароль тот же, который вы ввели при подготовке кластера.
5. На панели мониторинга щелкните вкладку **Редактор Hive** . Откроется следующая веб-страница.
   
    ![Вкладка «Редактор Hive» на панели мониторинга кластера HDInsight.][img-hdi-dashboard]
   
    В верхней части страницы содержится несколько вкладок: вкладка по умолчанию — **Hive Editor** (Редактор Hive) и остальные вкладки — **Журнал заданий** и **Браузер файлов**. С помощью панели мониторинга можно отправлять запросы Hive, проверять журналы заданий Hadoop и просматривать файлы в хранилище.
   
   > [!NOTE]
   > Обратите внимание, что у веб-страницы следующий URL-адрес: *&lt;ClusterName&gt;.azurehdinsight.net*. Таким образом, вместо открывания панели мониторинга на портале, можно открыть ее в веб-браузере, используя этот URL-адрес.
   > 
   > 
6. На вкладке **Hive Editor** (Редактор Hive) в поле **Имя запроса** введите **HTC20**.  Имя запроса представляет собой название задания. На панели запросов введите запрос Hive, как показано на рисунке:
   
    ![Запрос, введенный в области запроса на вкладке "Редактор Hive".][img-hdi-dashboard-query-select]
7. Нажмите кнопку **Submit**(Отправить). На получение результатов уходит несколько минут. Экран обновляется каждые 30 секунд. Чтобы обновить экран, можно также нажать кнопку **Обновить** .
   
    ![Результаты запроса Hive, указанные в нижней части панели инструментов кластера.][img-hdi-dashboard-query-select-result]
8. Когда состояние покажет, что задание завершено, щелкните имя запроса на экране, чтобы просмотреть выходные данные. Отметьте **Время запуска задания (формат UTC)**. Оно понадобится вам позднее.
   
    ![Строка "Время запуска задания", указанная на вкладке "Журнал заданий" на панели мониторинга кластера HDInsight.][img-hdi-dashboard-query-select-result-output]
   
    На странице также отображаются **выходные данные задания** и **журнал заданий**. Кроме этого, можно также скачать выходной файл (\_stdout) и файл журнал(\(_stderr).

**Переход к выходному файлу**

1. На панели мониторинга кластера щелкните **Браузер файлов**.
2. Последовательно щелкните имя учетной записи, имя контейнера (совпадает с именем кластера) и **пользователь**.
3. Щелкните **admin** , а затем щелкните GUID, время последнего изменения которого немного позднее ранее отмеченного времени запуска задания. Скопируйте этот GUID. Он понадобится в следующем разделе.

       ![The Hive query output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


## <a name="connect-to-microsoft-business-intelligence-tools-for-excel"></a>Подключение к средствам бизнес-аналитики Microsoft для Excel
С помощью надстройки Power Query для Microsoft Excel можно импортировать выходные данные задания из HDInsight в Excel, где для дальнейшего анализа результатов могут использоваться инструменты бизнес-аналитики Microsoft.

Для выполнения действий из этой части учебника требуется Excel 2013 или Excel 2010.

**Скачивание Microsoft Power Query для Excel**

* Загрузите Microsoft Power Query для Excel из [Центра загрузки Майкрософт](http://www.microsoft.com/download/details.aspx?id=39379) и установите.

**Импорт данных HDInsight**

1. Откройте Excel и создайте новую книгу.
2. Щелкните меню **Power Query**, выберите **Из других источников**, а затем щелкните **From Azure HDInsight** (Из Azure HDInsight).
   
    ![Меню "Импорт" в Power Query для Excel, открытое для Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]
3. В поле **Имя учетной записи** введите имя учетной записи хранилища BLOB-объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. (Это учетная запись хранения, которую вы ранее создали в этом учебнике.)
4. В поле **Ключ учетной записи** введите ключ для учетной записи хранилища BLOB-объектов Azure, а затем щелкните **Сохранить**.
5. В области справа дважды щелкните имя большого двоичного объекта. По умолчанию имя большого двоичного объекта совпадает с именем кластера.
6. Найдите **stdout** в столбце **Имя**. Проверьте, чтобы GUID в соответствующем столбце **Путь к папке** совпадал с GUID, который вы скопировали ранее. Совпадение предполагает, что выходные данные соответствуют отправленному заданию. Щелкните **Двоичный** в столбце слева от **stdout**.
   
    ![Поиск выходных данных в списке содержимого по GUID.][image-hdi-gettingstarted-powerquery-importdata2]
7. В верхнем левом углу щелкните **Закрыть и загрузить**, чтобы импортировать результаты задания Hive в Excel.

## <a name="run-samples"></a>Выполнение примеров
Кластер HDInsight предоставляет консоль запросов, содержащую коллекцию материалов для начала работы. Она предназначена для запуска примеров непосредственно с портала. Образцы могут использоваться для обучения работе с HDInsight путем пошагового прохождения некоторых базовых сценариев. Эти образцы поставляются вместе со всеми необходимыми компонентами, такими как данные для анализа и запросы для их запуска на основе этих данных. Дополнительные сведения о примерах в коллекции материалов для начала работы см. в статье [Изучение Hadoop с помощью коллекции для начала работы с Azure HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Запуск образца**

1. На начальной панели портала Azure щелкните плитку кластера, который вы только что создали.
2. В колонке нового кластера щелкните **Панель мониторинга**. При появлении запроса введите имя и пароль администратора для кластера.
   
    ![Запуск панели мониторинга кластера](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.Cluster.Dashboard.png "Запуск панели мониторинга кластера")
3. На открывшейся веб-странице выберите вкладку **Getting Started Gallery** (Коллекция для начала работы), а затем в категории **Solutions with Sample Data** (Решения с примерами данных) щелкните пример, который нужно выполнить. Следуйте инструкциям на веб-странице, чтобы завершить выполнение примера. В следующей таблице содержится несколько примеров, а также дополнительная информация о функциях каждого из них.

| Образец | Функция |
| --- | --- |
| [Анализ данных датчика][hdinsight-sensor-data-sample] |С помощью этого примера можно узнать об использовании HDInsight для обработки исторических данных, полученных системами отопления, вентиляции и кондиционирования воздуха, чтобы определить системы, которые не в состоянии надежно поддерживать заданную температуру. |
| [Анализ журнала веб-сайта][hdinsight-weblogs-sample] |С помощью этого примера можно узнать об использовании HDInsight для анализа файлов журнала веб-сайта, позволяющего получить представление о дневной частоте посещений веб-сайта с внешних сайтов, а также сводку об ошибках веб-сайта, с которыми столкнулись пользователи. |
| [Анализ тенденций Twitter](hdinsight-analyze-twitter-data.md) |С помощью этого примера можно узнать об использовании HDInsight для анализа тенденций в Twitter. |

## <a name="delete-the-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства по Hadoop вы узнали, как создать кластер Hadoop в Windows с помощью HDInsight, выполнить запрос Hive с данными и импортировать результаты в Excel для дальнейшей обработки и графического отображения с использованием инструментов бизнес-аналитики. Дополнительную информацию см. в следующих учебниках:

* [Приступая к работе с инструментами Azure Data Lake (в HDInsight) для Visual Studio для выполнения запроса Hive][1]
* [Начало работы в экосистеме Hadoop с песочницей Hadoop на виртуальной машине][hdinsight-emulator]
* [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
* [Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование MapReduce в Hadoop в HDInsight][hdinsight-use-mapreduce]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux][hdinsight-develop-mapreduce]

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-open]: /powershell/azureps-cmdlets-docs#step-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png




<!--HONumber=Dec16_HO2-->


