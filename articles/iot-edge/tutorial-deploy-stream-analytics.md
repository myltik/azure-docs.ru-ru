---
title: "Развертывание Azure Stream Analytics для Azure IoT Edge | Документация Майкрософт"
description: "Развертывание Azure Stream Analytics в качестве модуля для устройства Edge"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)

Устройства Интернета вещей могут производить большие объемы данных. Иногда эти данные необходимо проанализировать или обработать, прежде чем в облаке уменьшится размер переданных данных или будет удалена задержка кругового пути действенного анализа.

[Azure Stream Analytics][azure-stream] (ASA) предоставляет расширенный синтаксис структурированных запросов для анализа данных как в облаке, так и на устройствах IoT Edge. Дополнительные сведения об ASA в IoT Edge см. в [документации ASA](../stream-analytics/stream-analytics-edge.md).

В этом руководстве описывается, как создать задание Azure Stream Analytics и развернуть его на устройство IoT Edge для обработки потока локальных данных телеметрии непосредственно на устройстве и создания предупреждений для выполнения немедленных действий на устройстве.  В этом руководстве используются два модуля. Модуль имитированного датчика температуры (tempSensor), генерирующий данные температуры от 20 до 120 градусов, увеличивающиеся на 1 градус каждые 5 секунд, и модуль ASA, который отфильтровывает значения температуры, превышающие 100 градусов. Модуль ASA также сбрасывает tempSensor, когда среднее значение за 30 секунд достигает 100 градусов.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание задания ASA для обработки данных в Edge;
> * подключение нового задания ASA с другими модулями IoT Edge;
> * развертывание задания ASA на устройстве IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

* Центр Интернета вещей. 
* Устройство, созданное и настроенное с помощью действий в кратком руководстве или статье о развертывании Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] и [Linux][lnk-tutorial1-lin].
* Docker на вашем устройстве IoT Edge.
    * [Установите Docker в Windows][lnk-docker-windows] и убедитесь, что он выполняется.
    * [Установите Docker в Linux][lnk-docker-linux] и убедитесь, что он выполняется.
* Python 2.7.x на вашем устройстве IoT Edge.
    * [Установите Python 2.7 в Windows][lnk-python].
    * Большинство дистрибутивов Linux, включая Ubuntu, уже содержат установленный пакет Python 2.7.  Следующая команда позволит убедиться в том, что pip установлен: `sudo apt-get install python-pip`.

> [!NOTE]
> Запишите строку подключения устройства и идентификатор устройства IoT Edge, так как они понадобятся при прохождении этого руководства.

В IoT Edge используются встроенные модули службы Azure IoT Edge для быстрого развертывания, и Azure Stream Analytics (ASA) является одним из таких модулей. Вы можете создать задание ASA на портале, а затем перейти на портал Центра Интернета вещей, чтобы развернуть его в качестве модуля IoT Edge.  

Дополнительные сведения об Azure Stream Analytics см. в разделе **Обзор** [документации по Azure Stream Analytics][azure-stream].

## <a name="create-an-asa-job"></a>Создание задания ASA

В этом разделе создается задание Azure Stream Analytics для получения данных из вашего Центра Интернета вещей, запроса данных телеметрии, отправленных с вашего устройства, и передачи результатов в контейнер хранилища Azure (большой двоичный объект). Дополнительные сведения см. в разделе **Обзор** [документации по Azure Stream Analytics][azure-stream]. 

> [!NOTE]
> Для предоставления конечной точки, которая будет использоваться в качестве выходных данных в задании ASA, требуется учетная запись хранения Azure. В следующем примере используется тип хранилища BLOB-объектов.  Дополнительные сведения см. в разделе **BLOB-объекты** [документации по службе хранилища Azure][azure-storage].

1. На портале Azure выберите **Создать ресурс -> Хранилище**, щелкните **Просмотреть все** и выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь**.

2. Введите имя для вашей учетной записи хранения и выберите расположение, в котором хранится ваш Центр Интернета вещей. Щелкните **Создать**. Запишите имя для использования в дальнейшем.

    ![новая учетная запись хранения][1]

3. На портале Azure перейдите к только что созданной учетной записи хранения. Выберите **Обзор BLOB-объектов** в разделе **Служба BLOB-объектов**. 
4. Создайте контейнер для модуля ASA для хранения данных. Установите для уровня доступа значение _Контейнер_. Нажмите кнопку **ОК**.

    ![параметры хранилища][10]

5. На портале Azure перейдите к **Создать ресурс** > **Интернет вещей** и выберите **Задание Stream Analytics**.

2. Введите имя, выберите **Edge** в качестве среды размещения и используйте остальные значения по умолчанию.  Щелкните **Создать**.

    >[!NOTE]
    >В настоящее время задания ASA в IoT Edge не поддерживаются в регионе "Западная часть США 2". Будьте добры, выберите другое расположение.

    ![создание ASA][5]

2. Перейдите в созданное задание, в разделе **Топология задания** выберите **Входные данные** и нажмите кнопку **Добавить**.

3. Введите имя `temperature`, выберите **Поток данных** в качестве типа источника и используйте значения по умолчанию для остальных параметров. Щелкните **Создать**.

    ![входные данные ASA][2]

    > [!NOTE]
    > Дополнительные входы могут включать определенные конечные точки IoT Edge.

4. В разделе **Топология задания** выберите **Выходные данные** и нажмите кнопку **Добавить**.

5. Введите имя `alert` и используйте значения по умолчанию. Щелкните **Создать**.

    ![выходные данные ASA][3]

6. В разделе **Топология задания** выберите **Запрос** и введите следующее:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Развертывание задания

Теперь вы готовы к развертыванию задания ASA на вашем устройстве IoT Edge.

1. На портале Azure в Центре Интернета вещей перейдите в **IoT Edge (предварительная версия)** и откройте колонку вашего устройства *{ИД_устройства}*.

1. Щелкните **Set modules** (Задать модули), а затем — **Import Azure Service IoT Edge Module** (Импорт модуля IoT Edge службы Azure).

1. Выберите подписку и созданное задание ASA Edge. Затем выберите свою учетную запись хранения. Щелкните **Сохранить**.

    ![настройка модуля][6]

1. Нажмите кнопку **Add IoT Edge Module** (Добавить модуль IoT Edge), чтобы добавить модуль датчика температуры. Введите _tempSensor_ для имени, `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` для URL-адреса образа. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.

    ![модуль температуры][11]

1. Скопируйте имя модуля ASA. Нажмите кнопку **Далее**, чтобы настроить маршруты.

1. Скопируйте следующие данные в поле **Маршруты**.  Замените _{moduleName}_ именем скопированного модуля:

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

1. Щелкните **Далее**.

1. На шаге **Review Template** (Проверка шаблона) щелкните **Отправить**.

1. Вернитесь на страницу сведений об устройстве и нажмите **Обновить**.  Вы должны увидеть новый модуль _{moduleName}_, работающий вместе с модулем **агента IoT Edge** и **концентратором IoT Edge**.

    ![выходные данные модуля][7]

## <a name="view-data"></a>Просмотр данных

Теперь можно перейти к устройству IoT Edge, чтобы проверить взаимодействие между модулями ASA и tempSensor.

1. В командной строке настройте среду выполнения с помощью строки подключения устройства IoT Edge:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Выполните следующую команду, чтобы запустить среду выполнения:

    ```cmd/sh
    iotedgectl start  
    ```

1. Выполните следующую команду, чтобы просмотреть выполняющиеся модули:

    ```cmd/sh
    docker ps  
    ```

    ![выходные данные docker][8]

1. Выполните команду, чтобы просмотреть все системные журналы и данные метрик. Используйте имя модуля выше:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![журнал docker][9]

1. На портале Azure в вашей учетной записи хранения в разделе **Служба BLOB-объектов** нажмите кнопку **Обзор BLOB-объектов**, выберите контейнер и только что созданный файл JSON.

1. Нажмите кнопку **Загрузить** и просмотрите результаты.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы настроили контейнер службы хранилища Azure и задание Streaming Analytics для анализа данных с устройства IoT Edge.  Затем вы загрузили пользовательский модуль ASA, чтобы переместить данные с устройства через поток в большой двоичный объект для загрузки.  Вы можете перейти к изучению других руководств, чтобы в дальнейшем увидеть, как Azure IoT Edge может создавать решения для вашего бизнеса.

> [!div class="nextstepaction"] 
> [Развертывание Машинного обучения Azure в качестве модуля IoT Edge — предварительная версия][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
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
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/