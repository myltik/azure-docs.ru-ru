---
title: Развертывание Azure Stream Analytics для Azure IoT Edge | Документация Майкрософт
description: Развертывание Azure Stream Analytics в качестве модуля для устройства Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c94652017216bd9c8ff319e0b19fa3597c75e81c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)

Устройства Интернета вещей могут производить большие объемы данных. Иногда, чтобы уменьшить размер переданных данных или исключить задержку кругового пути действенного анализа, эти данные необходимо проанализировать или обработать до перехода в облако.

В Azure IoT Edge используются встроенные модули службы Azure IoT Edge для быстрого развертывания. [Azure Stream Analytics][azure-stream] является одним из таких модулей. Вы можете создать задание Azure Stream Analytics на портале, а затем перейти на портал Центра Интернета вещей Azure, чтобы развернуть его в качестве модуля IoT Edge. 

Azure Stream Analytics предоставляет расширенный синтаксис структурированных запросов для анализа данных как в облаке, так и на устройствах IoT Edge. Дополнительные сведения об Azure Stream Analytics в IoT Edge см. в статье [Azure Stream Analytics в IoT Edge (предварительная версия)](../stream-analytics/stream-analytics-edge.md).

В этом руководстве рассматривается создание задания Azure Stream Analytics и его развертывание на устройстве IoT Edge. Это позволяет обработать поток локальной телеметрии непосредственно на устройстве и создать предупреждения, которые запускают немедленное действие на устройстве. 

В этом руководстве предоставлено два модуля. 
* Имитированный модуль датчика температуры (tempSensor), который генерирует значения температуры от 20 до 120 градусов, увеличивающиеся на 1 градус каждые 5 секунд. 
* Модуль Stream Analytics, который сбрасывает tempSensor, когда среднее значение за 30 секунд достигает 70 градусов. В рабочей среде вы можете использовать эту функцию для выключения компьютера или принятия профилактических мер, когда температура достигает опасных уровней. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание задания Azure Stream Analytics для обработки пограничных данных.
> * Подключение нового задания Azure Stream Analytics с другими модулями IoT Edge.
> * Развертывать задания Azure Stream Analytics на устройстве IoT Edge.

## <a name="prerequisites"></a>предварительным требованиям

* Центр Интернета вещей. 
* Устройство, созданное и настроенное с помощью действий в кратком руководстве или статье о развертывании Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Необходимо знать ключ подключения устройства и идентификатор устройства. 
* На вашем устройстве IoT Edge должен выполняться компонент Docker.
    * [Установите Docker для Windows][lnk-docker-windows].
    * [Установите Docker для Linux][lnk-docker-linux].
* Python 2.7.x на вашем устройстве IoT Edge.
    * [Установите Python 2.7 в Windows][lnk-python].
    * Большинство дистрибутивов Linux, включая Ubuntu, уже содержат установленный пакет Python 2.7. Проверить, установлена ли система pip, можно с помощью следующей команды: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Создание задания Azure Stream Analytics

В этом разделе создается задание Azure Stream Analytics для получения данных из вашего Центра Интернета вещей, запроса данных телеметрии, отправленных с вашего устройства, и передачи результатов в контейнер хранилища BLOB-объектов Azure. Дополнительные сведения см. в разделе "Обзор" [документации по Azure Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Для предоставления конечной точки, которая будет использоваться в качестве выходных данных в задании Azure Stream Analytics, требуется учетная запись хранения Azure. В примере в этом разделе используется тип хранилища BLOB-объектов. Дополнительные сведения см. в разделе "BLOB-объекты" [документации по службе хранилища Azure][azure-storage].

1. На портале Azure выберите **Создать ресурс**, введите **Учетная запись хранения** в поле поиска, а затем выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь**.

2. В области **Создать учетную запись хранения** введите имя для своей учетной записи хранения, выберите расположение, где хранится ваш Центр Интернета вещей, а затем щелкните **Создать**. Запишите имя для использования в дальнейшем.

    ![Создайте учетную запись хранения.][1]

3. Перейдите к созданной учетной записи хранения и выберите **Обзор BLOB-объектов**. 

4. Создайте контейнер для модуля Azure Stream Analytics для хранения данных. Задайте уровень доступа **Container** (Контейнер), а затем выберите **ОК**.

    ![Параметры хранилища][10]

### <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На портале Azure выберите **Создать ресурс** > **Интернет вещей**, а затем — **Задание Stream Analytics**.

2. В области **Новое задание Stream Analytics** выполните следующие действия.

    a. В поле **Job name** (Имя задания) введите имя задания.
    
    Б. В разделе **Среда размещения**выберите **Edge**.
    
    c. В остальных полях используйте значения по умолчанию.

    > [!NOTE]
    > Сейчас задания Azure Stream Analytics в IoT Edge не поддерживаются в регионе "Западная часть США 2". 

3. Нажмите кнопку **Создать**.

4. В созданном задании в разделе **Топология задания** выберите **Inputs** (Входные данные) и щелкните **Добавить**.

5. В области **Новые входные данные** выполните следующие действия.

    a. В поле **Входной псевдоним** введите **температуру**.
    
    Б. В поле **Source Type** (Тип источника) выберите **Поток данных**.
    
    c. В остальных полях используйте значения по умолчанию.

   ![Входные данные Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Нажмите кнопку **Создать**.

7. В разделе **Топология задания** выберите **Outputs** (Выходные данные) и нажмите **Добавить**.

8. В области **Новое выходное значение** выполните следующие действия.

    a. В поле **Выходной псевдоним** введите **предупреждение**.
    
    Б. В остальных полях используйте значения по умолчанию. 
    
    c. Нажмите кнопку **Создать**.

   ![Выходные данные Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. В разделе **Топология задания**выберите **Query** (Запрос) и замените текст по умолчанию следующим запросом.

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

10. Щелкните **Сохранить**.

## <a name="deploy-the-job"></a>Развертывание задания

Теперь вы готовы к развертыванию задания Azure Stream Analytics на своем устройстве IoT Edge.

1. На портале Azure в Центре Интернета вещей перейдите к **IoT Edge (предварительная версия)**, а затем откройте страницу сведений о своем устройстве IoT Edge.

2. Щелкните **Set modules** (Настроить модули).  
    Если модуль tempSensor на этом устройстве уже развернут, эти данные могут быть заполнены автоматически. Если же он не установлен, добавьте модуль следующим образом.

   a. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).

   Б. В качестве имени введите **tempSensor**.
    
   c. В качестве универсального кода ресурса (URI) образа введите **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**. 

   d. Оставьте без изменений другие параметры.
   
   д. Щелкните **Сохранить**.

3. Чтобы добавить задание Edge Azure Stream Analytics, выберите **Import Azure Stream Analytics IoT Edge Module** (Импортировать модуль IoT Edge для Azure Stream Analytics).

4. Выберите свою подписку и созданное задание Edge Azure Stream Analytics. 

5. Выберите свою подписку и созданную учетную запись хранения, а затем щелкните **Save** (Сохранить).

    ![Настройка модуля][6]

6. Скопируйте имя модуля Azure Stream Analytics. 

    ![Модуль температуры][11]

7. Выберите **Next** (Далее), чтобы настроить маршруты.

8. Скопируйте в поле **Routes** (Маршруты) следующий код. Замените _{moduleName}_ скопированным именем модуля.

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

9. Щелкните **Далее**.

10. В окне **Review Template** (Проверка шаблона) выберите **Отправить**.

11. Вернитесь на страницу сведений об устройстве и выберите **Refresh** (Обновить).  
    Вы должны увидеть новый модуль Stream Analytics, работающий вместе с модулем агента IoT Edge и концентратором IoT Edge.

    ![Выходные данные модуля][7]

## <a name="view-data"></a>Просмотр данных

Теперь можно перейти к устройству IoT Edge, чтобы проверить взаимодействие между модулями Azure Stream Analytics и tempSensor.

1. Проверьте выполнение всех модулей в Docker.

   ```cmd/sh
   docker ps  
   ```

   ![Выходные данные Docker][8]

2. Просмотрите все системные журналы и данные метрик. Используйте имя модуля Stream Analytics.

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Вы должны видать, как температура компьютера постепенно растет, пока не достигнет 70 градусов на 30 секунд. Затем модуль Stream Analytics активирует сброс, и температура компьютера уменьшается до 21 градуса. 

   ![Журнал Docker][9]


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы настроили контейнер службы хранилища Azure и задание Streaming Analytics для анализа данных из устройства IoT Edge. Затем вы загрузили пользовательский модуль Azure Stream Analytics, чтобы переместить данные с устройства через поток в большой двоичный объект для загрузки. Вы можете перейти к изучению других руководств, чтобы узнать, как Azure IoT Edge может создавать решения для вашего бизнеса.

> [!div class="nextstepaction"] 
> [Развертывание Машинного обучения Azure в качестве модуля IoT Edge — предварительная версия][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
