---
title: Запуск записной книжки Databricks с помощью действия Databricks Notebook в фабрике данных Azure
description: Сведения об использовании действия Databricks Notebook в фабрике данных Azure для выполнения записной книжки Databricks в кластере заданий Databricks.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 78954e2dd00e425d2dfdd81d2c3e386f199f4f8f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311052"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Запуск записной книжки Databricks с помощью действия Databricks Notebook в фабрике данных Azure

В этом руководстве рассматривается создание конвейера фабрики данных Azure, который выполняет записную книжку Databricks в кластере заданий Databricks, на портале Azure. Конвейер также передает параметры фабрики данных Azure в записную книжку Databricks во время выполнения.

В этом руководстве вы выполните следующие шаги:

  - Создадите фабрику данных.

  - создадите конвейер, который использует действие Databricks Notebook.

  - Активация выполнения конвейера.

  - Выполнение мониторинга выполнения конвейера.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

Уделите 11 минут вашего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>предварительным требованиям

  - **Создайте рабочую область Azure Databricks**. [Создайте рабочую область Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) или используйте существующую. Вы создадите записную книжку Python в рабочей области Azure Databricks. Затем запустите ее и передадите в нее параметры, используя фабрику данных Azure.

## <a name="create-a-data-factory"></a>Создание фабрики данных

1.  Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.

2.  В меню слева выберите **Создать**, **Данные+аналитика**, **Фабрика данных**.

    ![Создание фабрики данных](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  На панели **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**.

    Имя фабрики данных Azure должно быть *глобально уникальным*. Если появится следующая ошибка, измените имя фабрики данных. (Например, используйте **\<yourname\>ADFTutorialDataFactory**.) Правила именования для артефактов службы "Фабрика данных" см. в [этой](https://docs.microsoft.com/azure/data-factory/naming-rules) статье.

    ![Указание имени для новой фабрики данных](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  В поле **Подписка** выберите подписку Azure, в рамках которой вы хотите создать фабрику данных.

5.  Для **группы ресурсов** выполните одно из следующих действий:
    
    - Выберите **Использовать существующий** и выберите существующую группу ресурсов из раскрывающегося списка.
    
    - Выберите **Создать** и введите имя группы ресурсов.

    Некоторые действия, описанные в этом руководстве, предполагают, что для группы ресурсов используется имя **ADFTutorialResourceGroup**. Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  Для **версии** выберите **V2 (предварительная версия)**.

2.  В поле **Расположение** выберите расположение фабрики данных.

    Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (такие как служба хранилища Azure и база данных SQL Azure) и вычислительные среды (например, HDInsight), используемые фабрикой данных, могут находиться в других регионах.

3.  Кроме того, установите флажок **Закрепить на панели мониторинга**.

4.  Нажмите кнопку **Создать**.

5.  На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных).

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  Когда создание завершится, откроется страница **Фабрика данных**. Выберите элемент **Author & Monitor** (Создание и мониторинг), чтобы открыть на отдельной вкладке приложение пользовательского интерфейса службы "Фабрика данных".

    ![Запуск приложения пользовательского интерфейса фабрики данных](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Создание связанных служб

В этом разделе создается связанная служба Databricks. Эта связанная служба содержит сведения о подключении к кластеру Databricks.

### <a name="create-an-azure-databricks-linked-service"></a>Создание связанной службы Azure Databricks

1.  На странице **Let's get started** (Начало работы) переключитесь на вкладку **Edit** (Редактирование) на левой панели.

    ![Изменение новой связанной службы](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  В нижней части окна выберите **Подключения**, а затем **+ Создать**.
    
    ![Создание подключения](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  В окне **New Linked Service** (Новая связанная служба) выберите **Хранилище данных** \> **Azure Databricks** и щелкните **Продолжить**.
    
    ![Указание связанной службы Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  В окне **New Linked Service** (Новая связанная служба) сделайте следующее:
    
    1.  В качестве **имени** введите ***AzureDatabricks\_LinkedService***.
    
    2.  В разделе **Select cluster** (Выберите кластер) выберите **New Cluster** (Новый кластер).
    
    3.  В поле **Domain/Region** (Домен или регион) выберите регион, в котором находится рабочее пространство Azure Databricks.
    
    4.  В поле **Cluster node type** (Тип узла кластера) выберите **Standard\_D3\_v2** для этого руководства.
    
    5.  В поле **Маркер доступа** укажите маркер, сгенерированный из рабочей области Azure Databricks. Инструкции можно найти [здесь](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  В поле **Версия кластера** выберите **бета-версию 4.0** (последняя версия).
    
    7.  В качестве **числа рабочих узлов** введите **2**.
    
    8.  Нажмите кнопку **Готово**.

        ![Завершение создания связанной службы](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Создание конвейера

1.  Нажмите кнопку **+** (плюс) и в меню выберите **Pipeline** (Конвейер).

    ![Кнопки для создания конвейера](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Создайте **параметр** для использования в **конвейере**. Позже вы передадите этот параметр в действие Databricks Notebook. В пустом конвейере щелкните вкладку **Параметры**, а затем — **Создать** и присвойте параметру имя **name**.

    ![Создание параметра](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Создание параметра name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  На панели элементов **Действия** разверните узел **Databricks**. Перетащите действие **Notebook** с панели элементов **Действия** в область конструктора конвейера.

    ![Перетаскивание записной книжки в область конструктора](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  В свойствах для окна действия **Databricks** **Notebook** в нижней части страницы выполните следующие действия.

    a. Переключитесь на вкладку **Параметры** .

    Б. Выберите **myAzureDatabricks\_LinkedService** (которая была создана в предыдущей процедуре).

    c. Выберите **путь к записной книжке** Databricks. Давайте создадим записную книжку и укажем путь. Чтобы получить путь к записной книжке, выполните следующие действия.

       1. Запуск рабочей области Azure Databricks

       2. В рабочей области выберите команду **создания папки** и присвойте папке имя **adftutorial**.

          ![Создание папки](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Создайте записную книжку](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) с именем **mynotebook** в папке **adftutorial****,** выбрав команду **Создать**.

          ![Создание записной книжки](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Указание свойств новой записной книжки](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. В недавно созданной записной книжке mynotebook добавьте следующий код:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Создание мини-приложений для параметров](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. В этом случае **путь к записной книжке** — **/adftutorial/mynotebook**.

5.  Вернитесь к **инструменту для создания пользовательского интерфейса фабрики данных**. Перейдите на вкладку **Параметры** в разделе **Действие Notebook1**. 
    
    a.  **Добавьте параметр** в действие записной книжки. Это тот же параметр, который был добавлен ранее в **конвейер**.

       ![Добавление параметра](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    Б.  Присвойте параметру имя **input** и укажите в качестве значения выражение **@pipeline().parameters.name**.

6.  Чтобы проверить работу конвейера, нажмите кнопку **Проверка** на панели инструментов. Чтобы закрыть окно проверки, нажмите кнопку **\>\>** (стрелка вправо).

    ![Проверка конвейера](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Выберите **Опубликовать все**. Пользовательский интерфейс фабрики данных опубликует сущности (связанные службы и конвейер) в службе фабрики данных Azure.

    ![Публикация новых сущностей фабрики данных](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Активация выполнения конвейера

Выберите **Trigger** (Запустить) на панели инструментов, а затем **Trigger Now** (Запустить сейчас).

![Выбор команды Trigger Now (Запустить сейчас)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

В диалоговом окне **Pipeline Run** (Запуск конвейера) появится запрос на ввод параметра **name**. Укажите **/path/filename** в качестве параметра. Нажмите кнопку **Готово**.

![Указание значения для параметра name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Выполнили мониторинг конвейера.

1.  Перейдите на вкладку **Мониторинг**. Убедитесь, что здесь отображается запуск конвейера. Для создания кластера заданий Databricks, где выполняется записная книжка, требуется приблизительно 5–8 минут.

    ![Мониторинг конвейера](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Периодически нажимайте **Обновить**, чтобы контролировать состояние выполнения конвейера.

3.  Чтобы увидеть выполнение действий, связанных с выполнением конвейера, выберите **View Activity Runs** (Просмотр выполнения действий) в столбце **Действия**.

    ![Просмотр выполнений действий](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Чтобы вернуться в режим просмотра запусков конвейера, выберите ссылку **Конвейеры** вверху.

## <a name="verify-the-output"></a>Проверка выходных данных

Войдите в **рабочую область Azure Databricks**, перейдите в раздел **Кластеры**, и вы увидите одно из таких состояний **задания**: *в ожидании, выполняется или завершено*.

![Просмотр кластера заданий и задания](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Щелкнув **имя задания**, можно перейти к дополнительным сведениям. При успешном запуске можно проверить переданные параметры и выходные данные записной книжки Python.

![Просмотр сведений о выполнении и выходных данных](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Дополнительная информация

В этом примере конвейер запускает действие Databricks Notebook и передает в него параметр. Вы научились выполнять следующие задачи:

  - Создадите фабрику данных.

  - создавать конвейер, который использует действие Databricks Notebook.

  - Активация выполнения конвейера.

  - Выполнение мониторинга выполнения конвейера.
