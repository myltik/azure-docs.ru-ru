---
title: Модуль Azure IoT Edge с кодом Python | Документация Майкрософт
description: Создание модуля IoT Edge с кодом Python и его развертывание на пограничном устройстве
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177841"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Разработка модуля IoT Edge с кодом Python и его развертывание на имитированном устройстве (предварительная версия)

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы используете имитированное устройство IoT Edge, созданное при работе с руководством по развертыванию Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять такие задачи:    

> [!div class="checklist"]
> * создание модуля Python для IoT Edge с помощью Visual Studio Code;
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикация в реестре. 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, созданные вашим устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

> [!IMPORTANT]
> В настоящее время модуль Python может выполняться только в контейнерах Linux на платформе AMD64. Он не может выполняться в контейнерах Windows или контейнеры на основе ARM. 

## <a name="prerequisites"></a>предварительным требованиям

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или первым руководством.
* Строка подключения первичного ключа для устройства IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Расширение Visual Studio Code для Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) на компьютере с Visual Studio Code. Для этого руководства достаточно выпуска Community Edition (CE). 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) для установки пакетов Python (обычно входит в состав установки Python).

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.
2. Укажите имя реестра, выберите подписку, выберите группу ресурсов и задайте номер SKU **Базовый**. 
3. Нажмите кнопку **Создать**.
4. После создания реестра контейнеров перейдите к нему и выберите **Ключи доступа**. 
5. **Включите** параметр **Пользователь-администратор**.
6. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения вам понадобятся позже при работе с этим руководством. 

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
Описанный ниже процесс позволяет создать модуль Python для IoT Edge с использованием Visual Studio Code и расширения Azure IoT Edge.
1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.
2. Введите приведенную ниже команду в интегрированном терминале, чтобы установить (или обновить) **cookiecutter** (мы советуем сделать это в виртуальной среде либо в качестве установки пользователя, как показано ниже).

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Создайте проект для нового модуля. Приведенная ниже команда создает папку проекта **FilterModule** в указанном репозитории контейнеров. Если вы используете реестр контейнеров Azure, параметр `image_repository` должен иметь формат `<your container registry name>.azurecr.io/filtermodule`. В текущей рабочей папке введите следующую команду:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Выберите **Файл** > **Открыть папку**.
5. Перейдите в папку **FilterModule** и щелкните **Выбрать папку**, чтобы открыть проект в VS Code.
6. В проводнике VS Code щелкните файл **main.py**, чтобы открыть его.
7. В верхней части пространства имен **FilterModule** выберите импорт библиотеки `json`:

    ```python
    import json
    ```

8. Добавьте `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` и `TWIN_CALLBACKS` в разделе глобальных счетчиков. Порог температуры задает значение измеренной температуры, при превышении которого данные отправляются в Центр Интернета вещей.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Обновите функцию `receive_message_callback`, присвоив ей приведенное ниже содержимое.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Добавьте функцию `device_twin_callback`. Эта функция будет вызываться при обновлении нужного свойства.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. В классе `HubManager` добавьте в метод `__init__` новую строку, которая инициализирует только что добавленную функцию `device_twin_callback`.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Сохраните этот файл.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала VS Code. 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Укажите имя пользователя, пароль и сервер входа, который был скопирован из реестра контейнеров Azure при его создании.

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **module.json** и выберите действие **Build and Push IoT Edge module Docker image** (Создать и отправить образ Docker для модуля IoT Edge). Во всплывающем раскрывающемся списке в верхней части окна VS Code выберите платформу контейнера, например **amd64** для контейнера Linux. VS Code поместит в контейнер файл `main.py` и все необходимые зависимости, затем передаст его в указанный вами реестр контейнеров. Первая сборка образа может длиться несколько минут.

3. Полный адрес образа контейнеров с тегом можно получить через интегрированный терминал VS Code. Дополнительные сведения об определении сборки и отправки см. в файле `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Добавление учетных данных реестра в среду выполнения Edge
Добавьте учетные данные для своего реестра в среду выполнения Edge на компьютере, где запущено устройство Edge. С их помощью среда выполнения сможет получить доступ для извлечения контейнера. 

- В ОС Windows выполните следующую команду:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- В ОС Linux выполните следующую команду:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Запуск решения

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com).
2. Щелкните **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
3. Щелкните **Set Modules** (Настроить модули). 
2. Убедитесь, что модуль **tempSensor** заполняется автоматически. Если это не так, следуйте инструкциям ниже, чтобы добавить его.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.
9. Добавьте модуль **filterModule**, созданный в предыдущих разделах. 
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `filterModule`.
    3. В поле **URI изображения** введите адрес образа, например `<your container registry address>/filtermodule:0.0.1-amd64`. Полный адрес образа можно найти в предыдущем разделе.
    4. Установите флажок **Включить**, чтобы получить возможность изменить двойник модуля. 
    5. Замените JSON-файл в текстовом поле для двойника модуля следующим JSON-файлом: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Выберите команду **Сохранить**.
10. Нажмите кнопку **Далее**.
11. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Модули публикуют все сообщения в среду выполнения Edge. Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры в модуль фильтра через конечную точку input1, которая настроена с помощью обработчика **FilterMessages**. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте `upstream` является специальным пунктом назначения, который говорит концентратору Edge отправлять сообщения в Центр Интернета вещей. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Нажмите кнопку **Далее**.
5. На шаге **Review Template** (Проверка шаблона) щелкните **Отправить**. 
6. Вернитесь на страницу сведений об устройстве IoT Edge и щелкните **Обновить**. Вы должны увидеть новый модуль **filtermodule**, работающий вместе с модулем **tempSensor** и **средой выполнения IoT Edge**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отслеживать сообщения между устройством и облаком, отправленные с вашего устройства IoT Edge в Центр Интернета вещей:
1. Настройте расширение Azure IoT Toolkit со строкой подключения для вашего Центра Интернета вещей: 
    1. Откройте обозреватель VS Code, выбрав **Вид** > **Обозреватель**. 
    3. В проводнике щелкните **IOT HUB DEVICES** (Устройства Центра Интернета вещей) и нажмите кнопку **...**. Щелкните **Set IoT Hub Connection String** (Настроить строку подключения Центра Интернета вещей) и введите во всплывающем окне строку подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge. 

        Чтобы найти строку подключения, щелкните плитку своего Центра Интернета вещей на портале Azure и выберите **Политики общего доступа**. В колонке **Политики общего доступа** выберите политику **iothubowner**, а затем скопируйте строку подключения Центра Интернета вещей в окне **iothubowner**.   

1. Чтобы отслеживать данные, поступающие в Центр Интернета вещей, выберите**Вид** > **Палитра команд** и щелкните пункт меню **IoT: Start monitoring D2C message** (Интернет вещей: начать мониторинг сообщений D2C). 
2. Чтобы перестать отслеживать данные, щелкните пункт меню **IoT: Stop monitoring D2C message** (Интернет вещей: остановить мониторинг сообщений D2C). 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Вы можете перейти к одному из следующих руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве

> [!div class="nextstepaction"]
> [Deploy Azure Function as an IoT Edge module - preview](tutorial-deploy-function.md)
>  (Развертывание функции Azure в виде модуля IoT Edge (предварительная версия))[Deploy Azure Stream Analytics as an IoT Edge module - preview](tutorial-deploy-stream-analytics.md) (Развертывание Azure Stream Analytics в виде модуля IoT Edge (предварительная версия))


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
