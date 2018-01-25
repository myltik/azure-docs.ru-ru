---
title: "Преобразование данных с помощью Spark в фабрике данных Azure | Документация Майкрософт"
description: "В этом руководстве представлены пошаговые инструкции по преобразованию данных с использованием действия Spark в фабрике данных Azure."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure
В этом руководстве вы с помощью портала Azure создадите конвейер фабрики данных, который преобразовывает данные с помощью действия Spark и связанной службы HDInsight по запросу. В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных. 
> * Создание конвейера с действием Spark
> * Активация выполнения конвейера
> * Выполнение мониторинга выполнения конвейера.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
* **Учетная запись хранения Azure.** Нужно создать входной файл и скрипт Python и передать их в хранилище Azure. Выходные данные программы Spark хранятся в этой учетной записи хранения. Кластер Spark по запросу использует ту же учетную запись хранения, что и его основное хранилище.  
* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Отправка скрипта Python в учетную запись хранилища BLOB-объектов
1. Создайте файл Python с именем **WordCount_Spark.py** со следующим содержимым: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Замените свойство **storageAccountName&lt;&gt;** именем своей учетной записи хранения Azure. Затем сохраните файл. 
3. В хранилище BLOB-объектов Azure создайте контейнер с именем **adftutorial**, если он не существует. 
4. Создайте папку с именем **spark**.
5. Создайте вложенную папку с именем **script** в папке **spark**. 
6. Отправьте файл **WordCount_Spark.py** во вложенную папку **script**. 


### <a name="upload-the-input-file"></a>Отправка входного файла
1. Создайте файл с определенным текстом и назовите его **minecraftstory.txt**. Программа Spark подсчитывает количество слов в этом тексте. 
2. Создайте вложенную папку с именем `inputfiles` в папке `spark`. 
3. Отправьте файл `minecraftstory.txt` во вложенную папку `inputfiles`. 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы увидите следующую ошибку для поля имени, введите другое имя фабрики данных (например, ваше_имя_ADFTutorialBulkCopyDF). Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
     ![Ошибка, связанная с недоступностью имени](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
      Некоторые действия, описанные в этом руководстве, предполагают, что для группы ресурсов используется имя **ADFTutorialResourceGroup**. Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V2 (Preview)** (V2 (предварительная версия)) для **версии**.
5. Укажите **расположение** фабрики данных. Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
    ![Домашняя страница фабрики данных](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. Щелкните плитку **Создание и мониторинг**, чтобы открыть на отдельной вкладке приложение пользовательского интерфейса фабрики данных.

## <a name="create-linked-services"></a>Создание связанных служб
Создайте две связанные службы в этом разделе: 
    
- **Связанную службу хранилища Azure**, которая связывает учетную запись хранения Azure с фабрикой данных. Это хранилище используется кластером HDInsight по запросу. В нем также содержится скрипт Spark для выполнения. 
- **Связанную службу HDInsight по запросу**. Фабрика данных Azure автоматически создает кластер HDInsight, запускает программу Spark, а затем удаляет кластер HDInsight после простоя в течение предварительно настроенного времени. 

### <a name="create-an-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure

1. На странице **начала работы** откройте вкладку **Изменить** на панели слева, как показано на следующем рисунке: 

    ![Плитка создания конвейера](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Щелкните **Подключения** в нижней части окна, а затем **+ Создать**. 

    ![Кнопка создания подключения](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. В окне **New Linked Service** (Новая связанная служба) выберите **хранилище BLOB-объектов Azure** и щелкните **Continue** (Продолжить). 

    ![Выбор хранилища BLOB-объектов Azure](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Выберите нужное **имя учетной записи хранения Azure** и щелкните **Save** (Сохранить). 

    ![Выбор учетной записи хранилища BLOB-объектов](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Создание связанной службы HDInsight по запросу

1. Снова нажмите кнопку **+ Создать**, чтобы создать еще одну связанную службу. 
2. В окне **New Linked Service** (Новая связанная служба) выберите **Azure HDInsight** и щелкните **Continue** (Продолжить). 

    ![Выбор Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. В окне **New Linked Service** (Новая связанная служба) выполните следующие действия: 

    1. Введите **AzureHDInsightLinkedService** в поле **имени**.
    2. Убедитесь, что в поле **Type** (Тип) выбран вариант **On-demand HDInsight** (HDInsight по запросу).
    3. Выберите **AzureStorage1** для параметра **Azure Storage Linked Service** (Связанная служба хранилища Azure). Эта та связанная служба, которую вы создали ранее. Если вы указали другое имя, выберите его в этом поле. 
    4. Выберите **spark** в качестве **типа кластера**.
    5. Введите **идентификатор субъекта-службы**, имеющего права на создание кластера HDInsight. Этому субъекту-службе должна быть назначена роли участника подписки или группы ресурсов, в которой создается кластер. Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md).
    6. Введите **ключ субъекта-службы**. 
    7. Выберите ту же группу ресурсов, которую вы использовали при создании фабрики данных для **группы ресурсов**. В этой группе ресурсов будет создан кластер HDInsight. 
    8. Разверните список **Тип ОС**.
    9. Введите **имя** для **пользователя** кластера. 
    10. Введите **пароль** для этого пользователя. 
    11. Выберите команду **Сохранить**. 

        ![Параметры связанной службы HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight имеет ограничение на общее количество ядер, которые можно использовать в каждом поддерживаемом регионе Azure. Для связанной службы HDInsight по требованию будет создан кластер HDInsight в расположении хранилища Azure, используемом в качестве его основного хранилища. Убедитесь, что имеется достаточное количество квот ядра для успешного создания кластера. Дополнительные сведения см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Создание конвейера

2. Нажмите кнопку со знаком "+" (плюс) и в открывшемся меню щелкните **Конвейер**.

    ![Меню создания конвейера](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. На панели инструментов **Действия** разверните **HDInsight** и перетащите действие **Spark** с панели **Действия** в область конструктора конвейера. 

    ![Перетаскивание действия Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. В свойствах для окна действия **Spark** в нижней части страницы выполните следующие действия: 

    1. Перейдите на вкладку **HDI Cluster** (Кластер HDI).
    2. Выберите службу **AzureHDInsightLinkedService**, которую вы создали на предыдущем шаге. 
        
    ![Выбор связанной службы HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Перейдите на вкладку **Script/Jar** (Сценарий или JAR-файл) и выполните здесь следующие действия: 

    1. Выберите значение **AzureStorage1** для параметра **Job Linked Service** (Связанная служба задания).
    2. Щелкните **Поиск в хранилище...**. 
    3. Перейдите к папке **adftutorial/spark/script**, выберите в ней файл **WordCount_Spark.py** и щелкните **Готово**.      

    ![Выбор скрипта Spark](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. Чтобы проверить работу конвейера, нажмите кнопку **Проверка** на панели инструментов. Щелкните **стрелку вправо** (>>), чтобы закрыть окно проверки. 
    
    ![Кнопка проверки](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Щелкните **Опубликовать**. Пользовательский интерфейс фабрики данных опубликует сущности (связанные службы и конвейер) в службе фабрики данных Azure. 
    
    ![Кнопка "Опубликовать"](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Активация выполнения конвейера
Щелкните **Триггер** на панели инструментов, а затем **Trigger Now** (Активировать сейчас). 

![Trigger Now (Активировать сейчас)](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

1. Перейдите на вкладку **Мониторинг**. Убедитесь, что здесь отображается запуск конвейера. Создание кластера Spark занимает около 20 минут. 

    ![Мониторинг выполнений конвейера](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Периодически нажимайте **Обновить**, чтобы контролировать состояние выполнения конвейера. 

    ![Состояние выполнения конвейера](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. Чтобы просмотреть запуски действий, связанные с этим запуском конвейера, щелкните действие **View Activity Runs** (Просмотр запусков действий) в столбце действий. Чтобы вернуться в режим просмотра запусков конвейера, щелкните ссылку **Конвейеры** вверху.

    ![Просмотр запусков действий](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Проверка выходных данных
Убедитесь, что целевой файл создается в папке spark/otuputfiles/wordcount для контейнера adftutorial. 

![Проверка выходных данных](./media/tutorial-transform-data-spark-portal/verity-output.png)

В этом файле должны содержаться все слова из входного текстового файла и число, обозначающее количество таких слов в этом файле. Например:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Дополнительная информация
Конвейер из этого примера преобразует данные с помощью действия Spark и связанной службы HDInsight по запросу. Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> * Создадите фабрику данных. 
> * Создание конвейера с действием Spark
> * Активация выполнения конвейера
> * Выполнение мониторинга выполнения конвейера.

Чтобы узнать, как преобразовать данные, запустив скрипт Hive в кластере Azure HDInsight, который находится в виртуальной сети, ознакомьтесь со следующим руководством. 

> [!div class="nextstepaction"]
> [Transform data in Azure Virtual Network using Hive activity in Azure Data Factory](tutorial-transform-data-hive-virtual-network-portal.md) (Преобразование данных в виртуальной сети Azure с помощью действия Hive в фабрике данных Azure).





