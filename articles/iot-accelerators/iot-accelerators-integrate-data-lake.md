---
title: Интеграция решения удаленного мониторинга с Azure Data Lake Store | Документация Майкрософт
description: Узнайте о том, как выполнить интеграцию решения удаленного мониторинга с Azure Data Lake Store с помощью задания Azure Stream Analytics.
+services: ''
+suite: iot-suite
+author: philmea
+manager: timlt
+ms.author: philmea
+ms.date: 04/029/2018
+ms.topic: article
+ms.service: iot-suite
ms.openlocfilehash: 426ded8079ba5d6de4c186a8d18dd284082b0c1c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Интеграция решения удаленного мониторинга с Azure Data Lake Store

Вам может понадобиться расширенная аналитика, которой нет в решении удаленного мониторинга. Azure Data Lake Store идеально подходит для этого приложения, так как оно может хранить крупные и разнообразные наборы данных, а также интегрироваться с Azure Data Lake Analytics, чтобы предоставлять данные аналитики по запросу.

В этом практическом руководстве используется задание Azure Stream Analytics для потоковой передачи данных из Центра Интернета вещей в вашем решении удаленного мониторинга в Azure Data Lake Store.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим практическим руководством необходимо следующее:

* [Развернуть предварительно настроенное решение для удаленного мониторинга](iot-accelerators-remote-monitoring-deploy.md).
  * Решение удаленного мониторинга развернет Центр Интернета вещей и задание Azure Stream Analytics, используемые в этой статье, в вашей подписке Azure.
* [Развернуть Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
  * Data Lake Store следует развернуть в том же регионе, что и решение удаленного мониторинга.
  * [Создайте папку](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) с именем "streaming" в вашей учетной записи.

## <a name="create-a-consumer-group"></a>Создание группы потребителей

Создайте выделенную группу потребителей в Центре Интернета вещей вашего решения удаленного мониторинга. Ее будет использовать задание Stream Analytics для потоковой передачи данных в Data Lake Store.

> [!NOTE]
> Группы потребителей используются приложениями для извлечения данных из Центра Интернета вещей Azure. Новая группа потребителей создается для каждых пяти потребителей выходных данных. Можно создать до 32 групп потребителей.

1. Войдите на портал Azure.

1. На портале Azure нажмите кнопку **Cloud Shell**.

    ![Значок запуска на портале](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Выполните следующую команду, чтобы создать группу потребителей:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Используйте имена группы ресурсов и Центра Интернета вещей из решения удаленного мониторинга.

## <a name="create-stream-analytics-job"></a>Создание задания Stream Analytics

Создайте задание Azure Stream Analytics для потоковой передачи данных из вашего Центра Интернета вещей в хранилище Azure Data Lake Store.

1. Нажмите кнопку **Создать ресурс**, выберите "Интернет вещей" в Marketplace и щелкните **Задание Stream Analytics**.

    ![Новое задание Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Введите имя задания и выберите нужную подписку и группу ресурсов.

1. Выберите расположение в ближайшем или в том же регионе, где находится Data Lake Store. Здесь мы используем регион "Восточная часть США".

1. Оставьте среду размещения по умолчанию (**Облако**).

1. Нажмите кнопку **Создать**.

    ![Создание задания Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Настройка задания Stream Analytics

1. Перейдите в **задание Stream Analytics** в группе ресурсов решения удаленного мониторинга.

1. На странице обзора щелкните **Входные данные**.

    ![Страница обзора](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Щелкните **Добавить потоковый вход** и выберите **Центр Интернета вещей** из раскрывающегося списка.

    ![Добавление входных данных](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. На вкладке "Новые входные данные" введите псевдоним **IoTHub**.

1. В раскрывающемся списке "Группа потребителей" выберите ранее созданную группу потребителей. Здесь мы используем **streamanalyticsjob**.

    ![Выбор входных данных](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Выберите команду **Сохранить**.

1. На странице обзора щелкните **Выходные данные**.

    ![Добавление Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Щелкните **Добавить** и выберите **Data Lake Store** из раскрывающегося списка.

    ![Добавление выходных данных](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. На вкладке "Новые выходные данные" введите псевдоним **DataLakeStore**.

1. Выберите учетную запись Data Lake Store, созданную ранее, и укажите структуру папок для потоковой передачи данных в хранилище.

1. В поле формата даты введите **/streaming/{дата}/{время}**. Оставьте стандартные значения формата даты (ГГГГ/ММ/ДД) и формата времени (ЧЧ).

    ![Указание структуры папок](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Щелкните **Авторизовать**.

    Авторизуйтесь в Data Lake Store для предоставления заданию Stream Analytics доступа на запись в файловой системе.

    ![Авторизация Stream Analytics в Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Появится всплывающее окно, и, как только оно закроется и процесс авторизации завершится, кнопка "Авторизовать" будет неактивна.

    > [!NOTE]
    > Если во всплывающем окне появится сообщение об ошибке, откройте новое окно браузера в анонимном режиме и повторите попытку.

1. Выберите команду **Сохранить**.

## <a name="edit-the-stream-analytics-query"></a>Изменение запроса Stream Analytics

Azure Stream Analytics использует SQL-подобный язык запросов для указания источника входных данных, выполняющего потоковую передачу данных, преобразования данных в необходимый вид и вывода данных в различные хранилища или пункты обработки.

1. На вкладке "Обзор" щелкните **Изменить запрос**.

    ![Изменить запрос](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. В редакторе запросов замените заполнители [YourOutputAlias] и [YourInputAlias] на псевдонимы выходных и входных данных, которые вы определили ранее.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Запрос Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Выберите команду **Сохранить**.
1. Нажмите кнопку **Да** для принятия изменений.

## <a name="start-the-stream-analytics-job"></a>Запуск задания Stream Analytics

1. На вкладке "Обзор" нажмите кнопку **Запуск**.

    ![Запускает задание Stream Analytics.](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. На вкладке "Запуск задания" щелкните **Настраиваемое**.

1. Установите настраиваемое время, чтобы вернуться на несколько часов для выборки данных с момента, когда устройство начало потоковую передачу.

1. Нажмите **Запуск**.

    ![Выбор настраиваемой даты](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Подождите, пока задание перейдет в состояние выполнения. Если увидите сообщения об ошибках, это может быть из-за вашего запроса, поэтому убедитесь, что синтаксис правильный.

    ![Выполнение задания](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Задание потоковой передачи начнет считывать данные из вашего Центра Интернета вещей и сохранять их в Data Lake Store. Отображение данных в вашем Data Lake Store может занять несколько минут.

## <a name="explore-the-streaming-data"></a>Анализ данных потоковой передачи

1. Перейдите в Data Lake Store.

1. На вкладке "Обзор" щелкните**Обозреватель данных**.

1. В обозревателе данных откройте папку **/streaming**. Вы увидите папки, созданные в формате "ГГГГ/ММ/ДД/ЧЧ".

    ![Анализ данных потоковой передачи](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Вы увидите JSON-файлы, один файл за каждый час.

    ![Анализ данных потоковой передачи](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Дальнейшие действия

Azure Data Lake Analytics можно использовать для выполнения анализа больших данных в наборах данных Data Lake Store. Дополнительные сведения см. в [документации по Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics).
