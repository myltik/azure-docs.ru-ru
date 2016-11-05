---
title: Подключение устройства с помощью C в mbed | Microsoft Docs
description: Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C, запущенного на устройстве mbed.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2016
ms.author: dobett

---
# Подключение устройства к предварительно настроенному решению для удаленного мониторинга (mbed)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Выполнение сборки и запуск примера решения на C
Ниже перечислены инструкции по подключению устройства [Freescale FRDM-K64F с поддержкой mbed][lnk-mbed-home] к решению для удаленного мониторинга.

### Подключение устройства mbed к сети и настольному компьютеру
1. Подключите устройство mbed к сети, используя кабель Ethernet. Это необходимо, так как примеру приложения требуется доступ к Интернету.
2. Чтобы подключить устройство mbed к настольному компьютеру, см. раздел [Приступая к работе с mbed][lnk-mbed-getstarted].
3. Если настольный компьютер работает под управлением Windows, см. раздел [Настройка компьютера][lnk-mbed-pcconnect] для настройки доступа к устройству mbed через последовательный порт.

### Создание проекта mbed и импорт примера кода
1. Откройте в веб-браузере [сайт для разработчиков](https://developer.mbed.org/) на портале mbed.org. Если у вас еще нет учетной записи, зарегистрируйтесь (бесплатно). Если учетная запись есть, используйте ее для входа. Щелкните **Compiler** (Компилятор) в правом верхнем углу страницы. Отобразится интерфейс *Рабочая область*.
2. Убедитесь, что в правом верхнем углу окна отображается аппаратная платформа, которую вы используете. В противном случае щелкните значок и выберите другую платформу.
3. Щелкните **Импорт** в главном меню. Затем щелкните ссылку **Click here** (Щелкните здесь), чтобы выполнить импорт с URL-адреса рядом со значком земного шара и логотипом mbed.
   
    ![][6]
4. Во всплывающем окне введите ссылку на пример кода https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ и щелкните **Импортировать**.
   
    ![][7]
5. В окне компилятора mbed вы увидите, что при импорте проекта также импортируются различные библиотеки. Некоторые из них предоставляются и обслуживаются командой разработчиков Azure IoT ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure\_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), остальные являются библиотеками сторонних производителей, доступными в каталоге библиотек mbed.
   
    ![][8]
6. Откройте файл remote\_monitoring\\remote\_monitoring.c и найдите в нем следующий код:
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
7. Измените [идентификатор устройства] и [ключ устройства] в соответствии с параметрами своего устройства, чтобы программа смогла подключиться к центру IoT. Используйте имя узла центра IoT для замены заполнителей [IoTHub Name] и [IoTHub Suffix, т.е. azure-devices.net]. Например, если имя узла центра IoT — **contoso.azure-devices.net**, то **contoso** — это **hubName** (имя центра), а все остальное — **hubSuffix** (суффикс центра):
   
    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
   
    ![][9]

### Разбор кода
Если вам интересно, как работает программа, в этом разделе описаны основные фрагменты примера кода. Если вы просто хотите запустить код, сразу перейдите к разделу [Сборка и запуск проекта](#buildandrun).

#### Определение модели
Для определения модели, которая указывает, какие сообщения устройство может отправлять в центр IoT и получать из центра IoT, в этом примере используется библиотека [сериализатора][lnk-serializer]. В этом примере в пространстве имен **Contoso** определяется модель **Thermostat**, которая определяет данные телеметрии **Temperature**, **ExternalTemperature** и **Humidity**, а также метаданные, такие как идентификатор устройства, свойства устройства и команды, на которые отвечает устройство:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

С определением модели связаны определения команд **SetTemperature** и **SetHumidity**, на которые отвечает устройство:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### Подключение модели к библиотеке
Функции **sendMessage** и **IoTHubMessage** содержат стандартный код для отправки данных телеметрии с устройства и связи сообщений из центра IoT с обработчиками команд.

#### Функция remote\_monitoring\_run
Функция **main** программы вызывает функцию **remote\_monitoring\_run**, когда приложение начинает выполнять поведение устройства в качестве клиента центра IoT устройства. Функция **remote\_monitoring\_run** главным образом состоит из вложенных пар функций:

* **platform\_init** и **platform\_deinit** выполняют специализированные операции инициализации и завершения работы платформы.
* **serializer\_init** и **serializer\_deinit** инициализируют и отменяют инициализацию библиотеки сериализатора.
* **IoTHubClient\_Create** и **IoTHubClient\_Destroy** создают дескриптор клиента **iotHubClientHandle**, используя учетные данные устройства для подключения к центру IoT.

В основной части функции **remote\_monitoring\_run** программа выполняет следующие операции с использованием дескриптора **iotHubClientHandle**:

* Создает экземпляр модели термостата Contoso и настраивает обратные вызовы сообщений для двух команд.
* Отправляет сведения о самом устройстве, включая поддерживаемые им команды, в центр IoT с помощью библиотеки сериализатора. Когда центр получает это сообщение, он изменяет состояние устройства на панели мониторинга с **Ожидание** на **Работает**.
* Запускает цикл **while**, который каждую секунду отправляет значения температуры, внешней температуры и влажности в центр IoT.

Для справки ниже приведен пример сообщения **DeviceInfo**, которое отправляется в центр IoT при запуске устройства.

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Для справки ниже приведен пример сообщения **Telemetry**, которое отправляется в центр IoT.

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Для справки ниже приведен пример **команды**, получаемой из центра IoT.

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>

### Сборка и запуск проекта
1. Щелкните **Скомпилировать**, чтобы собрать программу. Вы можете проигнорировать все предупреждения. Однако если при сборке возникают ошибки, исправьте их, прежде чем продолжить.
2. Если сборка выполнена успешно, то на веб-сайте компилятора mbed создается BIN-файл с именем вашего проекта и загружается на локальный компьютер. Скопируйте BIN-файл на устройство. При сохранении BIN-файла на устройство происходит перезапуск устройства и запуск программы, содержащейся в BIN-файле. Программу можно вручную перезапустить в любое время, нажав кнопку сброса на устройстве mbed.
3. Подключитесь к устройству с помощью клиентского SSH-приложения, такого как PuTTY. Вы можете посмотреть, какой последовательный порт использует устройство, в диспетчере устройств Windows.
   
    ![][11]
4. В PuTTY выберите тип подключения **Serial** (Последовательный). Устройство обычно подключается со скоростью 9600 бод, поэтому введите 9600 в поле **Speed** (Скорость). Затем щелкните **Открыть**.
5. Начнется выполнение программы. Если программа не запускается автоматически при подключении, возможно, нужно перезагрузить плату (для этого нажмите клавиши CTRL+BREAK или кнопку сброса на плате).
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer

<!---HONumber=AcomDC_0720_2016-->