<properties
   	pageTitle="Учебник для Linux: начало работы с Hadoop и Hive | Microsoft Azure"
   	description="Следуйте инструкциям этого учебника для Linux, чтобы начать работу с Hadoop в HDInsight. Узнайте, как подготовить кластеры в Linux и запросить данные с помощью Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/13/2016"
   	ms.author="jgao"/>

# Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux

> [AZURE.SELECTOR]
- [На основе Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
- [На основе Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

Узнайте, как создавать кластеры [Hadoop](http://hadoop.apache.org/) в HDInsight под управлением Linux и запускать задания Hive в HDInsight. [Apache Hive](https://hive.apache.org/) — это самый популярный компонент экосистемы Hadoop. Сейчас служба HDInsight поставляется в комплекте с 4 разными типами кластеров: [Hadoop](hdinsight-hadoop-introduction.md), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) и [Storm](hdinsight-storm-overview.md). Каждый тип кластера поддерживает свой набор компонентов. Все 4 типа кластеров поддерживают инфраструктуру Hive. Для получения информации о поддерживаемых компонентах в HDInsight см. статью [Новые возможности в версиях кластеров Hadoop, предоставляемых в HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### Предварительные требования

Для работы с этим руководством вам потребуется:

- **Подписка Azure**: чтобы создать бесплатную пробную учетную запись (на один месяц), перейдите по адресу [azure.microsoft.com/free](https://azure.microsoft.com/free).

## Создание кластера

Большинство заданий Hadoop — пакетные. Вы создаете кластер, выполняете несколько заданий, а затем удаляете его. В этом разделе мы создадим в HDInsight кластер Hadoop под управлением Linux, используя [шаблон Azure ARM](../resource-group-template-deploy.md). Полностью настраиваемый шаблон ARM упрощает создание ресурсов Azure, таких как HDInsight. Знакомство с шаблонами Azure ARM не является обязательным условием для работы с этим руководством. Сведения о других способах создания кластеров, а также о свойствах, используемых в этом руководстве, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Шаблон ARM, используемый в этом руководстве, находится в общедоступном контейнере BLOB-объектов, [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json).

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон ARM на портале Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/ru-RU/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. В колонке **Параметры** заполните следующие поля.

    ![Шаблон начала работы с HDInsight под управлением Linux на портале](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).

    - **Имя кластера**. Введите имя создаваемого кластера Hadoop.
    - **Имя для входа и пароль кластера**. По умолчанию для входа используется имя **admin**.
    - **Имя пользователя SSH и пароль**. По умолчанию используется имя **sshuser**. Это имя можно изменить. 
    
    При работе с этим руководством другие параметры являются необязательными. Их можно не изменять.
    
    У каждого кластера есть зависимость учетной записи хранения для хранилища BLOB-объектов Azure. Обычно она называется учетной записью хранения по умолчанию. Кластер HDInsight должен находиться в том же регионе Azure, что и его учетная запись хранения, используемая по умолчанию. Удаление кластеров не приведет к удалению учетной записи хранения. Имя учетной записи хранения в шаблоне — это имя кластера, к которому добавлено слово store.
    
3. Чтобы сохранить параметры, нажмите кнопку **ОК**.
4. В колонке **Настраиваемое развертывание** введите **имя новой группы ресурсов**, чтобы создать группу ресурсов. Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и т. д. Расположение группы ресурсов может отличаться от расположения кластера.
5. Щелкните **Условия использования**, а затем нажмите кнопку **Создать**.
6. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**. Вы увидите новый элемент под названием **Развертывание для развертывания шаблона**. Процесс создания кластера занимает около 20 минут. 
7.  После создания кластера заголовок элемента меняется на имя группы ресурсов, которое вы указали. Портал автоматически открывает две колонки с кластером и параметрами кластера. 

    ![Параметры кластера при начале работы с HDInsight под управлением Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).

    Отображаются два ресурса: кластер и учетная запись хранения по умолчанию.

##Выполнение запросов Hive

[Apache Hive](hdinsight-use-hive.md) — это самый популярный компонент службы HDInsight. Существует множество способов выполнения заданий Hive в HDInsight. В этом руководстве для выполнения некоторых заданий Hive вы будете использовать представление Hive Ambari из портала. Другие способы отправки заданий Hive описаны в статье [Использование Hive в HDInsight](hdinsight-use-hive.md).

1. Чтобы открыть Ambari, перейдите по ссылке **https://&lt;ClusterName>.azurehdinsight.net**, где &lt;ClusterName> — это кластер, который вы создали в предыдущем разделе.
2. Введите имя пользователя Hadoop и пароль, указанные в предыдущем разделе. Имя пользователя по умолчанию — **admin**.
3. Откройте **представление Hive**, как показано на снимке экрана ниже:

    ![Выбор представлений Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. На странице в разделе __Редактор запросов__ вставьте в рабочий лист следующие инструкции HiveQL:

		SHOW TABLES;

    >[AZURE.NOTE] Для Hive требуется точка с запятой.
        
5. Нажмите __Execute (Выполнить)__. Под окном редактора запросов появится раздел __Результаты обработки запроса__, содержащий сведения о выполнении задания.

    После выполнения запроса в разделе __Результаты обработки запроса__ будут отображены результаты операции. Вы увидите одну таблицу с именем **hivesampletable**. Этот пример таблицы Hive входит в состав всех кластеров HDInsight.

    ![Представления Hive в HDInsight](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

6. Повторите шаги 4 и 5 и выполните следующий запрос.

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] Обратите внимание на раскрывающийся список __Сохранить результаты__, расположенный в разделе __Результаты обработки запроса__ слева вверху. Этот список можно использовать для загрузки или сохранения результатов в хранилище HDInsight в формате CSV-файла.

7. Щелкните **журнал** для получения списка заданий.

Когда задание Hive будет завершено, вы сможете [экспортировать результаты в базу данных SQL Azure или базу данных SQL Server](hdinsight-use-sqoop-mac-linux.md) либо [визуализировать их с помощью Excel](hdinsight-connect-excel-power-query.md). Дополнительные сведения об использовании Hive в HDInsight см. в статье [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md).

##Очистка учебника

После завершения работы с этим руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры.

>[AZURE.NOTE] С помощью [фабрики данных Azure](hdinsight-hadoop-create-linux-clusters-adf.md) можно создать кластеры HDInsight по требованию, а также настроить параметр TimeToLive, чтобы автоматически удалять кластеры.

**Удаление кластера и/или учетной записи хранения по умолчанию**

1. Войдите на [портал Azure](https://portal.azure.com).
2. На панели мониторинга щелкните элемент с именем группы ресурсов, с помощью которой вы создали кластер.
3. Щелкните **Удалить** в колонке ресурсов, чтобы удалить группу ресурсов, которая содержит кластер и учетную запись хранения по умолчанию. Или щелкните имя кластера в элементе **Ресурсы** и щелкните **Удалить** в колонке кластера. Обратите внимание, что удаление группы ресурсов приведет к удалению учетной записи хранения. Если вы хотите сохранить учетную запись хранения, удалите только кластер.

## Дальнейшие действия

В этом руководстве вы узнали, как с помощью шаблона ARM создать кластер HDInsight под управлением Linux и как выполнять базовые запросы Hive.

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях.

- Дополнительные сведения об использовании Hive с HDInsight, включая выполнение запросов Hive из Visual Studio, см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].

- Дополнительные сведения о Pig, языке, который используется для преобразования данных, см. в статье [Использование Pig с HDInsight][hdinsight-use-pig].

- Дополнительные сведения о MapReduce, способе записи программ, которые обрабатывают данные в Hadoop, см. в статье [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].

- Дополнительные сведения об анализе данных в HDInsight с помощью средств HDInsight для Visual Studio см. в статье [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).

Если вы готовы приступить к работе с собственными данными и хотите узнать больше о том, как HDInsight сохраняет данные или как получать данные в HDInsight, см. следующие статьи:

- Информацию о том, как HDInsight использует хранилище BLOB-объектов Azure, см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Сведения об отправке данных в HDInsight см. в статье [Отправка данных в HDInsight][hdinsight-upload-data].

Дополнительные сведения о создании кластера HDInsight и управлении этим кластером см. в следующих статьях:

- Информацию об управлении кластером HDInsight под управлением Linux см. в статье [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md).

- Дополнительные сведения о параметрах, которые можно выбрать при создании кластера HDInsight, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

- Если вы уже знакомы с Linux и Hadoop, но хотите узнать об особенностях Hadoop в HDInsight, см. статью [Работа с HDInsight в Linux](hdinsight-hadoop-linux-information.md). Она содержит следующую информацию:

	* URL-адреса для служб, размещенных в кластере, например Ambari и WebHCat;
	* расположение файлов Hadoop и примеры в локальной файловой системе;
	* использование хранилища Azure (WASB) вместо HDFS в качестве хранилища данных по умолчанию.


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0615_2016-->