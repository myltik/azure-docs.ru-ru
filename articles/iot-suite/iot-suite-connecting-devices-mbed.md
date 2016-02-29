<properties
   pageTitle="Подключение устройства с помощью C в mbed | Microsoft Azure"
   description="Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C, запущенного на устройстве mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="dobett"/>


# Подключение устройства к предварительно настроенному решению для удаленного мониторинга IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Выполнение сборки и запуск примера решения на C в mbed

Ниже перечислены инструкции по подключению устройства [Freescale FRDM-K64F с поддержкой mbed][lnk-mbed-home] к решению для удаленного мониторинга.

### Подключение устройства к сети и настольному компьютеру

1. Подключите устройство mbed к сети, используя кабель Ethernet. Это необходимо, так как примеру приложения требуется доступ к Интернету.

2. Чтобы подключить устройство mbed к настольному компьютеру, см. раздел [Приступая к работе с mbed][lnk-mbed-getstarted].

3. Если настольный компьютер работает под управлением Windows, см. раздел [Настройка компьютера][lnk-mbed-pcconnect] для настройки доступа к устройству mbed через последовательный порт.

### Создание проекта mbed и импорт примера кода

1. Откройте в веб-браузере [сайт для разработчиков](https://developer.mbed.org/) на портале mbed.org. Если у вас еще нет учетной записи, зарегистрируйтесь (бесплатно). Если учетная запись есть, используйте ее для входа. Щелкните **Compiler** (Компилятор) в правом верхнем углу страницы. Откроется интерфейс управления рабочими областями.

2. Убедитесь, что в правом верхнем углу окна отображается аппаратная платформа, которую вы используете. В противном случае щелкните значок и выберите другую платформу.

3. Щелкните **Импорт** в главном меню. Затем щелкните ссылку **Click here** (Щелкните здесь), чтобы выполнить импорт с URL-адреса рядом со значком земного шара и логотипом mbed.

    ![][6]

4. Во всплывающем окне введите ссылку на пример кода https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ и щелкните **Импортировать**.

    ![][7]

5. В окне компилятора mbed вы увидите, что при импорте проекта были импортированы различные библиотеки. Некоторые из них предоставляются и обслуживаются командой разработчиков Azure IoT ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), остальные являются библиотеками сторонних производителей, доступными в каталоге библиотек mbed.

    ![][8]

6. Откройте файл remote\_monitoring\\remote\_monitoring.c и найдите в нем следующий код:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Замените [Device Id] и [Device Key] данными вашего устройства. Используйте имя узла центра IoT для замены заполнителей [IoTHub Name] и [IoTHub Suffix, т.е. azure-devices.net]. Например, если имя узла центра IoT — contoso.azure-devices.net, то contoso — это **hubName** (имя центра), а все остальное — **hubSuffix** (суффикс центра):

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### Сборка и запуск проекта

1. Щелкните **Скомпилировать**, чтобы собрать программу. Вы можете проигнорировать все предупреждения. Однако если при сборке возникают ошибки, исправьте их, прежде чем продолжить.

2. Если сборка выполнена успешно, то на веб-сайте компилятора mbed создается BIN-файл с именем вашего проекта и загружается на локальный компьютер. Скопируйте BIN-файл на устройство. При сохранении BIN-файла на устройство происходит перезапуск устройства и запуск программы, содержащейся в BIN-файле. Программу можно вручную перезапустить в любое время, нажав кнопку сброса на устройстве mbed.

3. Подключитесь к устройству с помощью клиентского SSH-приложения, такого как PuTTY. Вы можете посмотреть, какой последовательный порт использует устройство, в диспетчере устройств Windows.

    ![][11]

4. В PuTTY выберите тип подключения **Serial** (Последовательный). Устройство обычно подключается со скоростью 115200 бод, поэтому введите 115200 в поле **Скорость**. Затем щелкните **Открыть**.

5. Начнется выполнение программы. Если программа не запускается автоматически при подключении, возможно, нужно перезагрузить плату (для этого нажмите клавиши CTRL+BREAK или кнопку сброса на плате).

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

<!---HONumber=AcomDC_0218_2016-->