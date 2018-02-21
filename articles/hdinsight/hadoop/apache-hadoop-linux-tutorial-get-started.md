---
title: "Начало работы с Hadoop и Hive в Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как создать кластеры HDInsight и запросить данные с помощью Hive."
keywords: "начало работы с hadoop, hadoop под управлением linux, краткое руководство по hadoop, начало работы с hive, краткое руководство по hive"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: jgao
ms.openlocfilehash: a96612e09e51db8941a7828f8fe9cb7bbced837d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight

Узнайте, как создавать кластеры [Hadoop](http://hadoop.apache.org/) в HDInsight и запускать задания Hive в HDInsight. [Apache Hive](https://hive.apache.org/) — это самый популярный компонент экосистемы Hadoop. В настоящее время в HDInsight доступно [семь типов кластеров](apache-hadoop-introduction.md#overview). Каждый тип кластера поддерживает свой набор компонентов. Все типы кластеров поддерживают инфраструктуру Hive. Дополнительные сведения о поддерживаемых компонентах в HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется:

* **Подписка Azure**: чтобы создать бесплатную пробную учетную запись (на один месяц), перейдите по адресу [azure.microsoft.com/free](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Создание кластера

Большинство заданий Hadoop — пакетные. Вы создаете кластер, выполняете несколько заданий, а затем удаляете его. В этом разделе вы создадите в HDInsight кластер Hadoop, используя [шаблон Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Знакомство с шаблонами Resource Manager не является обязательным для работы с этим руководством. Сведения о других способах создания кластеров, а также о свойствах, используемых в этом руководстве, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

Используемый в этом руководстве шаблон Resource Manager расположен на портале [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Введите или выберите следующие значения:
   
    ![Шаблон Resource Manager для начала работы с HDInsight под управлением Linux на портале](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Развертывание кластера Hadoop в HDInsight с помощью портала Azure и шаблона диспетчера группы ресурсов")
   
    * **Подписка**. Выберите подписку Azure.
    * **Группа ресурсов.** Создайте группу ресурсов Azure или выберите имеющуюся.  Группа ресурсов — это контейнер компонентов Azure.  В этом случае группа ресурсов содержит кластер HDInsight и зависимую учетную запись службы хранилища Azure. 
    * **Расположение.** Выберите расположение Azure для создания кластера.  Выберите ближайшее к себе расположение для повышения производительности. 
    * **Тип кластера**. Для работы с этим руководством выберите **Hadoop**.
    * **Имя кластера**: введите имя кластера Hadoop. Так как все кластеры в HDInsight используют одно пространство имен DNS, это имя должно быть уникальным. Имя может содержать до 59 символов, включая буквы, цифры и дефисы. Обратите внимание, что первый и последний символы имени не могут быть дефисами.
    * **Имя для входа и пароль кластера**: имя для входа по умолчанию — **admin**.
    * **Имя пользователя SSH и пароль**: по умолчанию используется имя **sshuser**.  Это имя можно изменить. 
     
    Некоторые свойства жестко заданы в шаблоне.  Эти значения можно настроить из шаблона.
    
    У каждого кластера есть зависимость [учетной записи хранения Azure](../hdinsight-hadoop-use-blob-storage.md) или [учетной записи Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Она называется учетной записью хранения по умолчанию. Кластер HDInsight должен находиться в том же регионе Azure, что и его учетная запись хранения, используемая по умолчанию. Удаление кластеров не приведет к удалению учетной записи хранения. 
        
    Дополнительные сведения об этих свойствах см. в инструкциях по [созданию кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Установите флажок **Я принимаю указанные выше условия** и **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. На панели мониторинга портала появится новый элемент под названием **Deploying Template deployment** (Развертывание для развертывания шаблона). Процесс создания кластера занимает около 20 минут. 

4. После создания кластера заголовок элемента меняется на имя группы ресурсов, которое вы указали. Щелкните плитку, чтобы отобразить кластер и его хранилище по умолчанию в списке.
   
    ![Группа ресурсов при начале работы с HDInsight под управлением Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Группа ресурсов кластера Azure HDInsight")

4. Щелкните имя кластера, чтобы открыть его.

   ![Параметры кластера при начале работы с HDInsight под управлением Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Свойства кластера HDInsight")


## <a name="run-hive-queries"></a>Выполнение запросов Hive

[Apache Hive](hdinsight-use-hive.md) — это самый популярный компонент службы HDInsight. Существует множество способов выполнения заданий Hive в HDInsight. В этом руководстве используется представление Hive Ambari на портале. Другие способы отправки заданий Hive описаны в статье [Использование Hive в HDInsight](hdinsight-use-hive.md).

1. Чтобы открыть Ambari, как показано на предыдущем снимке экрана, щелкните **Панель мониторинга кластера** и **Панель мониторинга кластера HDInsight**.  Вы можете также перейти по ссылке **https://&lt;ClusterName>.azurehdinsight.net**, где &lt;ClusterName> — это кластер, созданный в предыдущем разделе.

2. Введите имя пользователя Hadoop и пароль, указанные при создании кластера. Имя пользователя по умолчанию — **admin**.

3. Откройте **представление Hive** , как показано на снимке экрана ниже:
   
    ![Выбор представлений Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Меню средства просмотра Hive в HDInsight")

4. На вкладке **Запрос** вставьте в лист следующие инструкции HiveQL:
   
        SHOW TABLES;

    ![Представление Hive в HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Редактор запросов представления Hive в HDInsight")
   
   > [!NOTE]
   > Для Hive требуется точка с запятой.       
   > 
   > 

5. Нажмите **Execute (Выполнить)**. Вкладка **Результаты** отобразится под вкладкой **Запрос** с информацией о задании. 
   
    Когда запрос будет выполнен, на вкладке **Запрос** появятся результаты этой операции. Вы увидите одну таблицу с именем **hivesampletable**. Этот пример таблицы Hive входит в состав всех кластеров HDInsight.
   
    ![Представление Hive в HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Редактор запросов представления Hive в HDInsight")

6. Повторите шаги 4 и 5 и выполните следующий запрос.
   
        SELECT * FROM hivesampletable;
   
7. Вы также можете сохранить результаты запроса. Нажмите кнопку справа и укажите, как это следует сделать: скачать результаты в качестве CSF-файла или сохранить их в учетной записи хранения, связанной с кластером.

    ![Save result of Hive query](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Сохранение результата запроса Hive")



Когда задание Hive будет завершено, вы сможете [экспортировать результаты в базу данных SQL Azure или базу данных SQL Server](apache-hadoop-use-sqoop-mac-linux.md) либо [визуализировать их с помощью Excel](apache-hadoop-connect-excel-power-query.md). Дополнительные сведения об использовании Hive в HDInsight см. в статье [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Очистка учебника
После завершения работы с этим руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. 

> [!NOTE]
> С помощью [фабрики данных Azure](../hdinsight-hadoop-create-linux-clusters-adf.md) можно создать кластеры HDInsight по требованию, а также настроить параметр TimeToLive, чтобы автоматически удалять кластеры. 
> 
> 

**Удаление кластера и/или учетной записи хранения по умолчанию**

1. Войдите на [портале Azure](https://portal.azure.com).
2. На панели мониторинга щелкните элемент с именем группы ресурсов, с помощью которой вы создали кластер.
3. Щелкните **Удалить группу ресурсов**, чтобы удалить группу ресурсов, которая содержит кластер и учетную запись хранения по умолчанию. Или щелкните имя кластера в элементе **Ресурсы**, а затем — **Удалить**. Обратите внимание, что удаление группы ресурсов приводит к удалению учетной записи хранения. Если вы хотите сохранить учетную запись хранения, удалите только кластер.

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы узнали, как с помощью шаблона Resource Manager создать кластер HDInsight под управлением Linux и как выполнять базовые запросы Hive.

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях.

* Дополнительные сведения об использовании Hive с HDInsight, включая выполнение запросов Hive из Visual Studio, см. в [этой статье](hdinsight-use-hive.md).
* Дополнительные сведения о Pig — языке, который используется для преобразования данных, — см. в статье [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).
* Дополнительные сведения о MapReduce, — способе написания программ, которые обрабатывают данные в Hadoop, — см. в статье [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md).
* Дополнительные сведения об анализе данных в HDInsight с помощью средств HDInsight для Visual Studio см. в статье [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](apache-hadoop-visual-studio-tools-get-started.md).

Если вы готовы приступить к работе с собственными данными и хотите узнать больше о том, как HDInsight сохраняет данные или как получать данные в HDInsight, см. сведения в статьях ниже:

* Сведения о том, как HDInsight использует службу хранилища Azure, см. в статье [Использование HDFS-совместимой службы хранилища с Hadoop в HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Сведения об отправке данных в HDInsight см. в статье [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md).

Дополнительные сведения о создании кластера HDInsight и управлении этим кластером см. в следующих статьях:

* Сведения об управлении кластером HDInsight под управлением Linux см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](../hdinsight-hadoop-manage-ambari.md).
* Дополнительные сведения о параметрах, которые можно выбрать при создании кластера HDInsight, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Если вы уже знакомы с Linux и Hadoop, но хотите узнать об особенностях Hadoop в HDInsight, см. статью [Сведения об использовании HDInsight в Linux](../hdinsight-hadoop-linux-information.md). Эта статья содержит следующую информацию:
  
  * URL-адреса для служб, размещенных в кластере, например Ambari и WebHCat;
  * расположение файлов Hadoop и примеры в локальной файловой системе;
  * использование хранилища Azure (WASB) вместо HDFS в качестве хранилища данных по умолчанию.

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


