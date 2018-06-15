---
title: Прогнозирование погоды с помощью машинного обучения Azure на основе данных Центра Интернета вещей | Документация Майкрософт
description: Используйте машинное обучение Azure, чтобы прогнозировать вероятность дождя на основе данных о температуре и влажности, которые собираются от датчиков в Центре Интернета вещей.
author: rangv
manager: ''
keywords: Прогнозирование погоды с помощью машинного обучения
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: a331f8a8a69ffe41a368c1b36f1680890aaac8bf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637673"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Прогнозирование погоды в машинном обучении Azure с помощью данных от датчиков Центра Интернета вещей

![Сквозная схема](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Машинное обучение — это метод обработки и анализа данных, который позволяет компьютеру на основе исторических данных прогнозировать ожидаемые поведения, результаты и тенденции. Служба машинного обучения Azure — это облачная служба прогнозной аналитики, которая позволяет быстро создавать и развертывать прогнозные модели в качестве решений аналитики.

## <a name="what-you-learn"></a>Что вы узнаете

Вы узнаете, как применить машинное обучение Azure для прогнозирования погоды (вероятности дождя) на основе данных о температуре и влажности, собранных в Центре Интернета вещей. Вероятность дождя вычисляется по заранее подготовленной модели прогнозирования погоды. Эта модель создается на основе исторических данных и применяется для оценки вероятности дождя, исходя из данных о температуре и влажности.

## <a name="what-you-do"></a>Что нужно сделать

- Развернем модель прогнозирования погоды как веб-службу.
- Добавить группу потребителей, чтобы обеспечить доступ к данным в Центре Интернета вещей.
- Создадим задание Stream Analytics и настроим его на выполнение следующих задач:
  - получение данных о температуре и влажности от Центра Интернета вещей;
  - вызов веб-службы для получения оценки вероятности дождя;
  - сохранение результатов в хранилище BLOB-объектов Azure;
- Откроем обозреватель хранилища Microsoft Azure для просмотра прогноза погоды.

## <a name="what-you-need"></a>Необходимые элементы

- Изучите руководство [Настройка вашего устройства](iot-hub-raspberry-pi-kit-node-get-started.md), где описаны следующие требования.
  - Активная подписка Azure.
  - Центр Интернета вещей Azure в подписке;
  - клиентское приложение, которое отправляет сообщения в Центр Интернета вещей Azure.
- учетную запись Студии машинного обучения Azure. ([Бесплатно ознакомьтесь с работой студии машинного обучения](https://studio.azureml.net/).)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Развертывание модели прогнозирования погоды как веб-службы

1. Откройте [страницу модели прогнозирования погоды](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Щелкните **Открыть в Studio** в Студии машинного обучения Microsoft Azure.
   ![Открытая страница модели прогнозирования погоды в коллекции Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Щелкните **Run** (Запуск), чтобы проверить действия модели. Выполнение этого шага может занять до 2 минут.
   ![Открытая модель прогнозирования погоды в Студии машинного обучения Azure](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Щелкните действие **SET UP WEB SERVICE** (Настроить веб-службу)  > **Predictive Web Service** (Прогнозная веб-служба).
   ![Развертывание модели прогнозирования погоды в Студии машинного обучения Azure](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. На схеме перетащите модуль **Web service input** (Вход веб-службы) куда-нибудь поближе к модулю **Score Model** (Оценка модели).
1. Соедините между собой модули **Web service input** (Вход веб-службы) и **Score Model** (Оценка модели).
   ![Соединение модулей в Студии машинного обучения Azure](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Щелкните **RUN** (Запуск), чтобы проверить действия модели.
1. Щелкните **DEPLOY WEB SERVICE** (Развернуть веб-службу), чтобы преобразовать эту модель в веб-службу.
1. На панели мониторинга модели скачайте **Excel 2010 or earlier workbook** (Книга Excel 2010 или более ранних версий) для действия **REQUEST/RESPONSE** (Запрос — ответ).

   > [!Note]
   > Обязательно используйте именно **Excel 2010 or earlier workbook** (Книга Excel 2010 или более ранних версий), даже если на вашем компьютере установлена более поздняя версия Excel.

   ![Скачивание книги Excel для конечной точки REQUEST RESPONSE ("запрос — ответ")](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Откройте эту книгу Excel и запишите параметры **WEB SERVICE URL** (URL веб-службы) и **ACCESS KEY** (Ключ доступа).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Создание, настройка и выполнение заданий Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На [портале Azure](https://portal.azure.com/) последовательно выберите **Создать ресурс** > **"Интернет вещей"** > **Stream Analytics job** (Задание Stream Analytics).
1. Введите представленные ниже сведения для задания.

   **Имя задания**. Имя задания. Оно должно быть глобально уникальным.

   **Группа ресурсов**. Выберите ту же группу ресурсов, которую использует Центр Интернета вещей.

   **Расположение**. Выберите то же расположение, которое использует группа ресурсов.

   **Закрепить на панели мониторинга**. Установите этот флажок, чтобы быстро открывать Центр Интернета вещей с помощью панели мониторинга.

   ![Создание задания Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Добавление входных данных в задание Stream Analytics

1. Откройте задание Stream Analytics.
1. В разделе **Топология задания** щелкните **Входные данные**.
1. В области **Входные данные** щелкните **Добавить**, а затем введите сведения, приведенные ниже.

   **Входной псевдоним**. Уникальный псевдоним для входных данных.

   **Источник**. Выберите **Центр Интернета вещей**.

   **Группа потребителей.** Выберите созданную группу потребителей.

   ![Добавление входных данных в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Добавление выходных данных в задание Stream Analytics

1. В разделе **Топология задания** щелкните **Выходные данные**.
1. В области **Выходные данные** щелкните **Добавить**, а затем введите сведения, приведенные ниже.

   **Выходной псевдоним**. Уникальный псевдоним для выходных данных.

   **Приемник.** Выберите **хранилище BLOB-объектов**.

   **Учетная запись хранения.** Это учетная запись хранения для вашего BLOB-объекта. Можно использовать существующую учетную запись хранения или создать новую.

   **Контейнер.** Это контейнер, в котором сохранен большой двоичный объект. Вы можете создать новый контейнер или использовать уже существующий.

   **Формат сериализации событий.** Выберите вариант **CSV**.

   ![Добавление выходных данных в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Добавление в задание Stream Analytics функции для вызова развернутой веб-службы

1. В разделе **Топология задания** щелкните **Функции** > **Добавить**.
1. Введите следующие сведения:

   **Псевдоним функции**: введите значение `machinelearning`.

   **Тип функции**: выберите вариант **Azure ML**.

   **Метод импорта**: выберите **Импортировать из другой подписки**.

   **URL-адрес**: введите URL веб-службы, который вы взяли из книги Excel.

   **Ключ**: введите ключ доступа, который вы взяли из книги Excel.

   ![Добавление функции в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Настройка запроса задания Stream Analytics

1. В разделе **Топология задания** щелкните **Запрос**.
1. Замените текст запроса следующим кодом.

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Замените значение `[YourInputAlias]` значением псевдонима входных данных задания.

   Замените значение `[YourOutputAlias]` значением псевдонима выходных данных задания.

1. Выберите команду **Сохранить**.

### <a name="run-the-stream-analytics-job"></a>Выполнение задания Stream Analytics

В задании Stream Analytics щелкните **Запуск** > **Сейчас** > **Запустить**. После успешного запуска состояние задания **Остановлено** изменится на **Выполняется**.

![Выполнение задания Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Использование обозревателя службы хранилища Microsoft Azure для просмотра прогноза погоды

Запустите клиентское приложение, чтобы начать сбор и отправку данных о температуре и влажности в Центр Интернета вещей. Для каждого сообщения, полученного Центром Интернета вещей, задание Stream Analytics вызывает веб-службу прогнозирования погоды, чтобы оценить вероятности дождя. Полученный результат сохраняется в хранилище BLOB-объектов Azure. Сохраненные результаты можно просмотреть с помощью обозревателя службы хранилища Azure.

1. [Скачайте и установите обозреватель хранилищ Microsoft Azure](http://storageexplorer.com/).
1. Откройте обозреватель службы хранилища Azure.
1. Войдите в учетную запись Azure.
1. Выберите свою подписку.
1. Щелкните подписку Azure > **Учетные записи хранения** > учетная запись хранения > **Контейнеры больших двоичных объектов** и выберите нужный контейнер.
1. Откройте CSV-файл, чтобы увидеть результат. Последний столбец содержит прогнозируемую вероятность дождя.

   ![Получение прогноза погоды с помощью машинного обучения Azure](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Сводка

Итак, вы успешно применили машинное обучение Azure, чтобы прогнозировать вероятность дождя на основе данных о температуре и влажности, которые получает Центр Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]