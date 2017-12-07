---
title: "Развертывание Azure Stream Analytics для Azure IoT Edge | Документация Майкрософт"
description: "Развертывание Azure Stream Analytics в качестве модуля для устройства Edge"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)

Устройства Интернета вещей могут производить большие объемы данных. Иногда эти данные необходимо проанализировать или обработать, прежде чем в облаке уменьшится размер переданных данных или будет удалена задержка кругового пути действенного анализа.

В IoT Edge используются встроенные модули службы Azure IoT Edge для быстрого развертывания, и [Azure Stream Analytics][azure-stream] (ASA) является одним из таких модулей. Вы можете создать задание ASA на портале, а затем перейти на портал Центра Интернета вещей, чтобы развернуть его в качестве модуля IoT Edge.  

Azure Stream Analytics предоставляет расширенный синтаксис структурированных запросов для анализа данных как в облаке, так и на устройствах IoT Edge. Дополнительные сведения об ASA в IoT Edge см. в [документации ASA](../stream-analytics/stream-analytics-edge.md).

В этом руководстве описывается, как создать задание Azure Stream Analytics и развернуть его на устройство IoT Edge для обработки потока локальных данных телеметрии непосредственно на устройстве и создания предупреждений для выполнения немедленных действий на устройстве.  В этом руководстве используются два модуля. Имитированный модуль датчика температуры (tempSensor) генерирует значения температуры от 20 до 120 градусов, увеличивающиеся на 1 градус каждые 5 секунд. Модуль Stream Analytics сбрасывает tempSensor, когда среднее значение за 30 секунд достигает 70 градусов. В рабочей среде эта функция может использоваться для выключения компьютера или принятия профилактических мер, когда температура достигает опасных уровней. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание задания ASA для обработки данных в Edge;
> * подключение нового задания ASA с другими модулями IoT Edge;
> * развертывание задания ASA на устройстве IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

* Центр Интернета вещей. 
* Устройство, созданное и настроенное с помощью действий в кратком руководстве или статье о развертывании Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] и [Linux][lnk-tutorial1-lin]. Необходимо знать ключ подключения устройства и идентификатор устройства. 
* На вашем устройстве IoT Edge должен выполняться компонент Docker.
    * [Установите Docker для Windows][lnk-docker-windows].
    * [Установите Docker для Linux][lnk-docker-linux].
* Python 2.7.x на вашем устройстве IoT Edge.
    * [Установите Python 2.7 в Windows][lnk-python].
    * Большинство дистрибутивов Linux, включая Ubuntu, уже содержат установленный пакет Python 2.7.  Следующая команда позволит убедиться в том, что pip установлен: `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>Создание задания ASA

В этом разделе создается задание Azure Stream Analytics для получения данных из вашего Центра Интернета вещей, запроса данных телеметрии, отправленных с вашего устройства, и передачи результатов в контейнер хранилища Azure (большой двоичный объект). Дополнительные сведения см. в разделе **Обзор** [документации по Azure Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Для предоставления конечной точки, которая будет использоваться в качестве выходных данных в задании ASA, требуется учетная запись хранения Azure. В следующем примере используется тип хранилища BLOB-объектов.  Дополнительные сведения см. в разделе **BLOB-объекты** [документации по службе хранилища Azure][azure-storage].

1. На портале Azure перейдите на страницу **Создать ресурс** и введите `Storage account` в строке поиска. Выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь**.

2. Введите имя своей учетной записи хранения и выберите расположение, в котором находится Центр Интернета вещей. Щелкните **Создать**. Запомните или запишите это имя для использования в дальнейшем.

    ![новая учетная запись хранения][1]

3. Перейдите к только что созданной учетной записи хранения. Щелкните **Обзор BLOB-объектов**. 
4. Создайте контейнер для модуля ASA для хранения данных. Установите для уровня доступа значение **Контейнер**. Нажмите кнопку **ОК**.

    ![параметры хранилища][10]

### <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На портале Azure перейдите к **Создать ресурс** > **Интернет вещей** и выберите **Задание Stream Analytics**.

2. Введите имя, выберите **Edge** в качестве среды размещения и используйте остальные значения по умолчанию.  Щелкните **Создать**.

    >[!NOTE]
    >В настоящее время задания ASA в IoT Edge не поддерживаются в регионе "Западная часть США 2". 

3. Перейдите к созданному заданию. Выберите **Входные данные** и щелкните **Добавить**.

4. В качестве входного псевдонима введите `temperature`, выберите **Поток данных** в качестве типа источника и используйте значения по умолчанию для остальных параметров. Щелкните **Создать**.

   ![входные данные ASA](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Выберите **Выходные данные** и щелкните **Добавить**.

6. В качестве входного псевдонима введите `alert` и используйте значения по умолчанию для остальных параметров. Щелкните **Создать**.

   ![выходные данные ASA](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Выберите **Запрос**.
8. Замените текст по умолчанию следующим запросом.

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
9. Щелкните **Сохранить**.

## <a name="deploy-the-job"></a>Развертывание задания

Теперь вы готовы к развертыванию задания ASA на вашем устройстве IoT Edge.

1. На портале Azure в Центре Интернета вещей перейдите к **IoT Edge (предварительная версия)** и откройте страницу сведений о своем устройстве IoT Edge.
1. Щелкните **Set modules** (Настроить модули).
1. Если модуль tempSensor на этом устройстве уже развернут, эти данные могут быть заполнены автоматически. Если это не так, следуйте инструкциям ниже, чтобы добавить этот модуль.
   1. Щелкните **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
   1. Введите имя `tempSensor` и универсальный код ресурса (URI) образа `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
   1. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.
1. Чтобы добавить задание Edge ASA, выберите **Import Azure Stream Analytics IoT Edge Module** (Импортировать модуль IoT Edge для Azure Stream Analytics).
1. Выберите свою подписку и созданное задание Edge ASA. 
1. Выберите свою подписку и созданную учетную запись хранения. Щелкните **Сохранить**.

    ![настройка модуля][6]

1. Скопируйте имя, который было автоматически создано для модуля ASA. 

    ![модуль температуры][11]

1. Нажмите кнопку **Далее**, чтобы настроить маршруты.
1. Скопируйте следующие данные в поле **Маршруты**.  Замените _{moduleName}_ скопированным именем модуля.

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

1. Вернитесь на страницу сведений об устройстве и нажмите **Обновить**.  Вы должны увидеть новый модуль Stream Analytics, работающий вместе с модулем **агента IoT Edge** и **концентратором IoT Edge**.

    ![выходные данные модуля][7]

## <a name="view-data"></a>Просмотр данных

Теперь можно перейти к устройству IoT Edge, чтобы проверить взаимодействие между модулями ASA и tempSensor.

Проверьте выполнение всех модулей в Docker.

   ```cmd/sh
   docker ps  
   ```

   ![выходные данные docker][8]

Просмотрите все системные журналы и данные метрик. Используйте имя модуля Stream Analytics.

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Вы должны видать, как температура компьютера постепенно растет, пока не достигнет 70 градусов на 30 секунд. Затем модуль Stream Analytics активирует сброс, и температура компьютера уменьшится до 21 градуса. 

   ![журнал docker][9]


## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы настроили контейнер службы хранилища Azure и задание Streaming Analytics для анализа данных с устройства IoT Edge.  Затем вы загрузили пользовательский модуль ASA, чтобы переместить данные с устройства через поток в большой двоичный объект для загрузки.  Вы можете перейти к изучению других руководств, чтобы в дальнейшем увидеть, как Azure IoT Edge может создавать решения для вашего бизнеса.

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