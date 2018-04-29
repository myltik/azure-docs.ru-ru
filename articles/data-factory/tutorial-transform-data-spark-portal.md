---
title: Преобразование данных с помощью Spark в фабрике данных Azure | Документация Майкрософт
description: В этом руководстве представлены пошаговые инструкции по преобразованию данных с использованием действия Spark в фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: e32fa771595fdc4bf5fe54ec14630961d467d40f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure
В этом руководстве вы создадите конвейер фабрики данных Azure с помощью портала Azure. Конвейер преобразует данные с помощью действия Spark и связанной службы Azure HDInsight по запросу. 

В этом руководстве вы выполните следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных. 
> * Создание конвейера, использующего действие Spark.
> * Активация выполнения конвейера.
> * Выполнение мониторинга выполнения конвейера.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь со статьей [Руководство. Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
* **Учетная запись хранения Azure**. Нужно создать входной файл и сценарий Python и передать их в службу хранилища Azure. Выходные данные программы Spark хранятся в этой учетной записи хранения. Кластер Spark по запросу использует ту же учетную запись хранения, что и его основное хранилище.  
* **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Отправка скрипта Python в учетную запись хранилища BLOB-объектов
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
2. Замените свойство *&lt;storageAccountName&gt;* именем своей учетной записи хранения Azure. Затем сохраните файл. 
3. В хранилище BLOB-объектов Azure создайте контейнер с именем **adftutorial**, если он не существует. 
4. Создайте папку с именем **spark**.
5. Создайте вложенную папку с именем **script** в папке **spark**. 
6. Отправьте файл **WordCount_Spark.py** во вложенную папку **script**. 


### <a name="upload-the-input-file"></a>Отправка входного файла
1. Создайте файл с определенным текстом и назовите его **minecraftstory.txt**. Программа Spark подсчитывает количество слов в этом тексте. 
2. Создайте вложенную папку с именем **inputfiles** в папке **spark**. 
3. Отправьте файл **minecraftstory.txt** во вложенную папку **inputfiles**. 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.
1. В меню слева выберите **Создать**, **Данные+аналитика**, **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. На панели **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
   ![Панель "Новая фабрика данных"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть *глобально уникальным*. Если появится следующая ошибка, измените имя фабрики данных. (Например, используйте **&lt;yourname&gt;ADFTutorialDataFactory**.) Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
  
   ![Ошибка "Имя недоступно"](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. В поле **Подписка** выберите подписку Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий:
     
   - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
   - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
   Некоторые действия, описанные в этом руководстве, предполагают, что для группы ресурсов используется имя **ADFTutorialResourceGroup**. Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
5. Для **версии** выберите **V2 (предварительная версия)**.
6. В поле **Расположение** выберите расположение фабрики данных. 

   Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (такие как служба хранилища Azure и база данных SQL Azure) и вычислительные среды (например, HDInsight), используемые фабрикой данных, могут находиться в других регионах.
7. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
8. Нажмите кнопку **Создать**.
9. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

   ![Элемент Deploying data factory (Развертывание фабрики данных)](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
10. Когда создание завершится, откроется страница **Фабрика данных**. Выберите элемент **Author & Monitor** (Создание и мониторинг), чтобы открыть на отдельной вкладке приложение пользовательского интерфейса службы "Фабрика данных".

    ![Домашняя страница фабрики данных с элементом Author & Monitor (Создание и мониторинг)](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Создание связанных служб
Создайте две связанные службы в этом разделе: 
    
- **Связанную службу хранилища Azure**, которая связывает учетную запись хранения Azure с фабрикой данных. Это хранилище используется кластером HDInsight по запросу. В нем также содержится скрипт Spark для выполнения. 
- **Связанную службу HDInsight по запросу**. Фабрика данных Azure автоматически создает кластер HDInsight и запускает программу Spark. Кластер Hadoop удаляется, если он не используется в течение заданного времени. 

### <a name="create-an-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure

1. На странице **Let's get started** (Начало работы) переключитесь на вкладку **Edit** (Редактирование) на левой панели. 

   ![Страница Let's get started (Начало работы)](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. В нижней части окна выберите **Подключения**, а затем **+ Создать**. 

   ![Кнопки для создания подключения](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. В окне **New Linked Service** (Новая связанная служба) выберите **Хранилище данных** > **Хранилище BLOB-объектов Azure** и щелкните **Продолжить**. 

   ![Выбор элемента "Хранилище BLOB-объектов Azure"](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Выберите имя из списка в поле **Имя учетной записи хранения**, а затем щелкните **Сохранить**. 

   ![Поле для указания имени учетной записи хранения](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Создание связанной службы HDInsight по запросу

1. Снова нажмите кнопку **+ Создать**, чтобы создать еще одну связанную службу. 
2. В окне **New Linked Service** (Новая связанная служба) выберите **Среда выполнения приложений** > **Azure HDInsight**, а затем выберите **Continue** (Продолжить). 

   ![Выбор элемента Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. В окне **New Linked Service** (Новая связанная служба) сделайте следующее: 

   a. В поле **Name** (Имя) введите **AzureHDInsightLinkedService**.
   
   Б. Убедитесь, что в поле **Type** (Тип) выбран вариант **On-demand HDInsight** (HDInsight по запросу).
   
   c. Для параметра **Azure Storage Linked Service** (Связанная служба хранилища Azure) выберите **AzureStorage1**. Эта та связанная служба, которую вы создали ранее. Если было использовано другое имя, укажите его в этом поле. 
   
   d. В качестве **типа кластера** выберите **spark**.
   
   д. В поле **Идентификатор субъекта-службы** введите идентификатор субъекта-службы, имеющего права на создание кластера HDInsight. 
   
      Этому субъекту-службе должна быть назначена роль участника подписки или группы ресурсов, в которой создается кластер. Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md).
   
   f. Введите ключ в поле **Ключ субъекта-службы**. 
   
   ж. В качестве **группы ресурсов** выберите ту же группу ресурсов, которая была использована при создании фабрики данных. В этой группе ресурсов будет создан кластер HDInsight. 
   
   h. Разверните список **Тип ОС**.
   
   i. Введите имя для пользователя кластера. 
   
   j. Введите пароль для этого пользователя. 
   
   k. Щелкните **Сохранить**. 

   ![Параметры связанной службы HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight ограничивает общее количество ядер, которые можно использовать в каждом поддерживаемом регионе Azure. Для связанной службы HDInsight по требованию создается кластер HDInsight в расположении хранилища Azure, используемом в качестве основного хранилища. Убедитесь, что имеется достаточное количество квот ядра для успешного создания кластера. Дополнительные сведения см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Создание конвейера

1. Нажмите кнопку **+** (плюс) и в меню выберите **Pipeline** (Конвейер).

   ![Кнопки для создания конвейера](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
2. На панели элементов **Действия** разверните узел **HDInsight**. Перетащите действие **Spark** с панели элементов **Действия** в область конструктора конвейера. 

   ![Перетаскивание действия Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
3. В свойствах для окна действия **Spark** в нижней части страницы завершите следующие действия: 

   a. Перейдите на вкладку **HDI Cluster** (Кластер HDI).
   
   Б. Выберите службу **AzureHDInsightLinkedService** (созданную в приведенной выше процедуре). 
        
   ![Определение связанной службы HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
4. Перейдите на вкладку **Script/Jar** (Скрипт или JAR-файл) и выполните следующие действия: 

   a. Для параметра **Job Linked Service** (Связанная служба задания) выберите значение **AzureStorage1**.
   
   Б. Выберите **Поиск в хранилище**.

   ![Определение сценария Spark на вкладке Script/Jar (Скрипт или JAR-файл)](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Перейдите к папке **adftutorial/spark/script**, выберите в ней файл **WordCount_Spark.py**, а затем выберите **Готово**.      

5. Чтобы проверить работу конвейера, нажмите кнопку **Проверка** на панели инструментов. Чтобы закрыть окно проверки, нажмите кнопку **>>** Стрелка вправо. 
    
   ![Кнопка Validate (Проверка)](./media/tutorial-transform-data-spark-portal/validate-button.png)
6. Выберите **Опубликовать все**. Пользовательский интерфейс фабрики данных опубликует сущности (связанные службы и конвейер) в службе фабрики данных Azure. 
    
   ![Кнопка "Опубликовать все"](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Активация выполнения конвейера
Выберите **Trigger** (Запустить) на панели инструментов, а затем **Trigger Now** (Запустить сейчас). 

![Кнопки "Trigger" (Запустить) и "Trigger Now" (Запустить сейчас)](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

1. Перейдите на вкладку **Мониторинг**. Убедитесь, что здесь отображается запуск конвейера. Создание кластера Spark занимает около 20 минут. 
   
2. Периодически нажимайте **Обновить**, чтобы контролировать состояние выполнения конвейера. 

   ![Вкладка для наблюдения за запуском конвейера с кнопкой "Обновить"](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

3. Чтобы увидеть выполнение действий, связанных с выполнением конвейера, выберите **View Activity Runs** (Просмотр выполнения действий) в столбце **Действия**.

   ![Состояние выполнения конвейера](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Чтобы вернуться в режим просмотра запусков конвейера, выберите ссылку **Конвейеры** вверху.

   ![Просмотр параметра "Выполнения действий"](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Проверка выходных данных
Убедитесь, что целевой файл создается в папке spark/otuputfiles/wordcount для контейнера adftutorial. 

![Расположение выходного файла](./media/tutorial-transform-data-spark-portal/verity-output.png)

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
> * Создание конвейера, использующего действие Spark.
> * Активация выполнения конвейера.
> * Выполнение мониторинга выполнения конвейера.

Чтобы узнать, как преобразовать данные, запустив сценарий Hive в кластере Azure HDInsight, который находится в виртуальной сети, ознакомьтесь со следующим руководством: 

> [!div class="nextstepaction"]
> [Преобразование данных в виртуальной сети Azure с помощью действия Hive в фабрике данных Azure](tutorial-transform-data-hive-virtual-network-portal.md).





