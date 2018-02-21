---
title: "Интеграция Azure Stream Analytics и Машинного обучения Azure | Документация Майкрософт"
description: "Использование определяемой пользователем функции и машинного обучения в задании Stream Analytics."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/06/2017
ms.author: samacha
ms.openlocfilehash: 243ee799d2cddb1baf5b8046eee6eaf182463d2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Выполнение анализа тональности с помощью Azure Stream Analytics и Машинного обучения Azure
В этой статье описывается, как быстро настроить простое задание Azure Stream Analytics, интегрированное с Машинным обучением Azure. Вы используете модель машинного обучения для анализа тональности из коллекции Cortana Intelligence для анализа потока текстовых данных, а также определения оценки тональности в реальном времени. С помощью Cortana Intelligence Suite вы сможете выполнить эту задачу, не вникая в особенности создания модели анализа тональности.

Вы сможете применить сведения, полученные в рамках этой статьи, в следующих сценариях:

* при анализе тональности в режиме реального времени в потоковых данных Twitter;
* при анализе записей разговоров клиента со специалистами службы поддержки;
* при оценке комментариев на форумах, в блогах и видео; 
* а также во многих других сценариях прогнозной оценки в реальном времени.

В реальном сценарии вы получите данные напрямую из потока данных Twitter. Чтобы упростить работу с этим руководством, мы описали сценарий, когда задание Streaming Analytics получает твиты из CSV-файла в хранилище BLOB-объектов Azure. Вы можете создать собственный CSV-файл или использовать пример этого файла, как показано на рисунке ниже.

![пример твитов в CSV-файле](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Задание Streaming Analytics, которое вы создаете, применяет модель анализа тональности как определяемую пользователем функцию к образцу текстовых данных из хранилища BLOB-объектов. Выходные данные (результат анализа тональности) записываются в то же хранилище BLOB-объектов в другом CSV-файле. 

На следующем рисунке показана эта конфигурация. Как указано, для повышения реалистичности сценария элемент ввода для хранилища BLOB-объектов можно заменить потоковыми данными Twitter из элемента ввода концентраторов событий Azure. Кроме того, можно создать визуализацию совокупной тональности [Microsoft Power BI](https://powerbi.microsoft.com/) в реальном времени.    

![Обзор интеграции машинного обучения в Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>предварительным требованиям
Чтобы начать, у вас должны быть следующие компоненты:

* Активная подписка Azure.
* CSV-файл с данными. Файл, показанный ранее, можно скачать с сайта [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv). Вы можете также создать собственный файл. В этой статьей предполагается использование файла из GitHub.

В общих чертах, чтобы выполнить задачи, описанные в этой статье, сделайте следующее.

1. Создайте учетную запись хранения Azure и контейнер хранилища BLOB-объектов, а затем передайте в контейнер входной файл в формате CSV.
3. Добавьте модель анализа тональности из коллекции Cortana Intelligence в рабочую область Машинного обучения Azure и разверните в ней эту модель как веб-службу.
5. Создайте задание Stream Analytics, вызывающее эту веб-службу как функцию, для определения тональности входного текста.
6. Запустите задание Stream Analytics и просмотрите выходные данные.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Создание контейнера хранилища и передача входного CSV-файла
Для этого шага можно использовать любой CSV-файл, например файл, доступный на GitHub.

1. На портале Azure щелкните **Создать** &gt; **Хранилище** &gt; **Учетная запись хранения**.

   ![создание новой учетной записи хранения](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-create-storage-account.png)

2. Введите имя (в примере используется `samldemo`). Имя должно быть уникальным в Azure и может содержать только строчные буквы и цифры. 

3. Укажите существующую группу ресурсов и расположение. Мы рекомендуем, чтобы все ресурсы, созданные в рамках этого руководства, использовали одно расположение.

    ![указание сведений об учетной записи хранения](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. На портале Azure выберите учетную запись хранения. В колонке учетной записи хранения щелкните **Контейнеры**, а затем щелкните **+&nbsp;Контейнер**, чтобы создать хранилище BLOB-объектов.

    ![создание контейнера больших двоичных объектов](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Укажите имя для контейнера (в примере это `azuresamldemoblob`) и выберите для **типа доступа** значение **BLOB-объект**. Закончив, нажмите кнопку **OK**.

    ![указание сведений о контейнере больших двоичных объектов](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. В колонке **Контейнеры** выберите новый контейнер, чтобы открыть его колонку.

7. Щелкните **Отправить**.

    ![Кнопка передачи для контейнера](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. В колонке **Отправить BLOB-объект** укажите CSV-файл, который вы хотите использовать для работы с этим руководством. В поле **Тип BLOB-объекта** выберите **Блочный BLOB-объект**, а для размера блока задайте значение 4 МБ (для работы с этим руководством это оптимальный вариант).

    ![отправка файла большого двоичного объекта](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

9. Нажмите кнопку **Передать** в нижней области колонки.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Добавление модели анализа тональности из коллекции Cortana Intelligence

Теперь, когда образец данных находится в большом двоичном объекте, вы можете включить модель анализа тональности в коллекции Cortana Intelligence.

1. Перейдите на страницу [модели прогнозной аналитики тональности](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) в коллекции Cortana Intelligence.  

2. Щелкните **Открыть в Studio**.  
   
   ![Машинное обучение и Stream Analytics, открытие Студии машинного обучения](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Выполните вход, чтобы перейти в рабочую область. Выберите расположение.

4. В нижней части страницы щелкните **Run** (Выполнить). Процесс выполнения занимает около минуты.

   ![выполнение эксперимента в Студии машинного обучения](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. После успешного выполнения процесса выберите кнопку **Deploy Web Service** (Развернуть веб-службу) в нижней области страницы.

   ![развертывание эксперимента как веб-службы в Студии машинного обучения](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Чтобы проверить, что модель анализа тональности готова к использованию, нажмите кнопку **Проверка**. Введите текст "Мне нравится Майкрософт". 

   ![проверка эксперимента в Студии машинного обучения](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Если проверка завершится успешно, вы увидите результат, аналогичный следующему:

   ![результаты проверки в Студии машинного обучения](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. В столбце **Приложения** щелкните ссылку **Excel 2010 or earlier workbook** (Книга Excel 2010 или более ранней версии), чтобы скачать книгу Excel. Книга содержит ключ API и URL-адрес, которые понадобятся вам позже для настройки задания Stream Analytics.

    ![Машинное обучение и Stream Analytics, сводка](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Создание задания Stream Analytics, использующего модель машинного обучения

Теперь вы можете создать задание Stream Analytics, считывающее пример твитов из CSV-файла в хранилище BLOB-объектов. 

### <a name="create-the-job"></a>Создание задания

1. Перейдите на [портал Azure](https://portal.azure.com).  

2. Щелкните **Создать** > **"Интернет вещей"** > **Задание Stream Analytics**. 

   ![Путь на портале Azure для перехода к новому заданию Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-new-iot-sa-job.png)
   
3. Назовите задание `azure-sa-ml-demo`, укажите подписку, существующую группу ресурсов (или создайте новую), а затем выберите расположение для задания.

   ![указание параметров для нового задания Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Настройка входных данных для задания
Задание получает входные данные из CSV-файла, переданного ранее в хранилище BLOB-объектов.

1. После создания задания в разделе **Топология задания** в колонке задания щелкните поле **Входные данные**.  
   
   ![Поле "Входные данные" в колонке задания Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

2. В колонке **Входные данные** нажмите кнопку **Добавить**.

   ![Кнопка "Добавить" для добавления входных данных в задание Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-button.png)  

3. Заполните колонку **Новые входные данные** следующими значениями:

    * **Входной псевдоним**: используйте имя `datainput`.
    * **Тип источника**: выберите **Поток данных**.
    * **Источник**: выберите **Хранилище BLOB-объектов**.
    * **Параметры импорта**: выберите **Использовать хранилище BLOB-объектов из текущей подписки**. 
    * **Учетная запись хранения**: выберите учетную запись хранения, созданную ранее.
    * **Контейнер**: выберите созданный ранее контейнер (`azuresamldemoblob`).
    * **Формат сериализации событий**: выберите **CSV**.

    ![Параметры для новых входных данных задания](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Нажмите кнопку **Создать**.

### <a name="configure-the-job-output"></a>Настройка выходных данных для задания
Задание передает результаты в то же хранилище BLOB-объектов, в котором получает входные данные. 

1. В разделе **Топология задания** в колонке задания щелкните поле **Выходные данные**.  
  
   ![Создание новых выходных данных для задания Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

2. В колонке **Выходные данные** щелкните **Добавить**, а затем добавьте выходные данные с псевдонимом `datamloutput`. 

3. Для поля **Приемник** выберите **Хранилище BLOB-объектов**. Укажите остальные параметры выходных данных, используя те же значения, которые использовались для хранилища BLOB-объектов для входных данных.

    * **Учетная запись хранения**: выберите учетную запись хранения, созданную ранее.
    * **Контейнер**: выберите созданный ранее контейнер (`azuresamldemoblob`).
    * **Формат сериализации событий**: выберите **CSV**.

   ![Параметры для новых выходных данных задания](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Нажмите кнопку **Создать**.   


### <a name="add-the-machine-learning-function"></a>Добавление функции машинного обучения 
Ранее вы опубликовали модель машинного обучения в веб-службе. В нашем сценарии выполняемое задание Stream Analytics отправляет каждый образец твита из входных данных в веб-службу для анализа тональности. Веб-служба машинного обучения возвращает тональность (`positive`, `neutral` или `negative`) и вероятность того, что твит положительный. 

В этом разделе руководства вы определите функцию в задании Stream Analytics. Эту функцию можно вызывать для отправки твита в веб-службу и получения ответа. 

1. У вас должны быть URL-адрес веб-службы и ключ API в скачанной ранее книге Excel.

2. Вернитесь в колонку обзора задания.

3. В разделе **Параметры** выберите **Функции**, а затем нажмите кнопку **Добавить**.

   ![Добавление функции в задание Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-function1.png) 

4. Введите `sentiment` как псевдоним для функции и заполните остальные параметры колонки следующими значениями.

    * **Тип функции**: выберите **Машинное обучение Azure**.
    * **Метод импорта**: выберите **Импортировать из другой подписки**. Это позволит вам ввести URL-адрес и ключ.
    * **URL-адрес**: вставьте URL-адрес веб-службы.
    * **Ключ**: вставьте ключ API.
  
    ![Параметры для добавления функции машинного обучения в задание Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
5. Нажмите кнопку **Создать**.

### <a name="create-a-query-to-transform-the-data"></a>Создание запроса для преобразования данных

Stream Analytics использует декларативный запрос на основе SQL для проверки входных данных и их обработки. В этом разделе вы создадите запрос, который считывает каждый твит из входных данных, а затем вызывает функцию машинного обучения для выполнения анализа тональности. Затем запрос отправляет результат в приемник выходных данных (хранилище BLOB-объектов).

1. Вернитесь в колонку обзора задания.

2.  В разделе **Топология задания** щелкните поле **Запрос**.

    ![Создание запроса для задания Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-query.png)  

3. Введите следующий запрос:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    Запрос вызывает созданную ранее функцию (`sentiment`) для выполнения анализа тональности каждого твита во входных данных. 

4. Щелкните **Сохранить** , чтобы сохранить запрос.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Запуск задания Stream Analytics и просмотр выходных данных

Теперь вы можете запустить задание Stream Analytics.

### <a name="start-the-job"></a>Запустите задание
1. Вернитесь в колонку обзора задания.

2. Нажмите кнопку **Запуск** в верхней области колонки.

    ![Создание запроса для задания Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/start-job.png)  

3. В диалоговом окне **Запуск задания** выберите **Настраиваемый** и укажите один день до передачи CSV-файла в хранилище BLOB-объектов. После этого нажмите кнопку **Запуск**.  


### <a name="check-the-output"></a>Просмотр выходных данных
1. Задание может выполняться несколько минут, прежде чем вы увидите выполнение этой операции в поле **Мониторинг**. 

2. Если у вас есть средство, которое вы обычно используете для просмотра содержимого хранилища BLOB-объектов, используйте его для просмотра контейнера `azuresamldemoblob`. Кроме того, на портале Azure выполните следующие действия:

    1. Найдите на портале учетную запись хранения `samldemo`, а в ней найдите контейнер `azuresamldemoblob`. Вы увидите в нем два файла: файл, содержащий образец твитов, и CSV-файл, созданный заданием Stream Analytics.
    2. Щелкните правой кнопкой мыши созданный файл, а затем выберите **Скачать**. 

   ![Скачивание выходных данных задания в виде CSV-файла из хранилища BLOB-объектов](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Откройте созданный CSV-файл. Вы должны увидеть примерно следующее:  
   
   ![Машинное обучение и Stream Analytics, просмотр CSV-файла](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Просмотр метрик
Вы можете также просматривать метрики, связанные с функциями Машинного обучения Azure. В поле **Мониторинг** в колонке задания отображаются следующие метрики, связанные с функцией:

* **Запросы функций** отображает количество запросов, отправленных к веб-службе машинного обучения.  
* **События функций** отображает количество событий в запросе. По умолчанию каждый запрос к веб-службе машинного обучения может содержать до 1000 событий.  


## <a name="next-steps"></a>Дополнительная информация

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Интеграция машинного обучения в Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



