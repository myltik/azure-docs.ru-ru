---
title: "Подключение устройства с помощью C в mbed | Документация Майкрософт"
description: "Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C, запущенного на устройстве mbed."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: ru-ru
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Выполнение сборки и запуск примера решения на C

Ниже перечислены инструкции по подключению устройства [Freescale FRDM-K64F с поддержкой mbed][lnk-mbed-home] к решению для удаленного мониторинга.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Подключение устройства mbed к сети и настольному компьютеру

1. Подключите устройство mbed к сети, используя кабель Ethernet. Это необходимо, так как примеру приложения требуется доступ к Интернету.

1. Чтобы подключить устройство mbed к настольному компьютеру, см. статью о [начале работы с mbed][lnk-mbed-getstarted].

1. Если настольный компьютер работает под управлением Windows, см. раздел о [настройке компьютера][lnk-mbed-pcconnect] для настройки доступа к устройству mbed через последовательный порт.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Создание проекта mbed и импорт примера кода

Выполните следующие действия, чтобы добавить пример кода в проект mbed. Мы импортируем начальный проект для удаленного мониторинга, а затем изменим проект для использования протокола MQTT вместо протокола AMQP. В настоящее время необходимо, чтобы протокол MQTT использовал функции управления устройствами Центра Интернета вещей.

1. Откройте в веб-браузере [сайт для разработчиков](https://developer.mbed.org/)на портале mbed.org. Если у вас еще нет учетной записи, зарегистрируйтесь (бесплатно). Если учетная запись есть, используйте ее для входа. Щелкните **Compiler** (Компилятор) в правом верхнем углу страницы. Отобразится интерфейс *Workspace* (Рабочая область).

1. Убедитесь, что в правом верхнем углу окна отображается аппаратная платформа, которую вы используете. В противном случае щелкните значок и выберите другую платформу.

1. Щелкните **Импорт** в главном меню. Щелкните ссылку **Click here to import from URL** (Щелкните здесь, чтобы импортировать с URL-адреса).
   
    ![Запуск импорта в рабочую область mbed][6]

1. Во всплывающем окне введите ссылку на пример кода https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/, а затем нажмите кнопку **Import** (Импорт).
   
    ![Импорт примера кода в рабочую область mbed][7]

1. В окне компилятора mbed вы увидите, что при импорте проекта также импортируются различные библиотеки. Некоторые из них предоставляются и обслуживаются командой разработчиков Центра Интернета вещей Azure ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), остальные являются библиотеками сторонних производителей, доступными в каталоге библиотек mbed.
   
    ![Просмотр проекта mbed][8]

1. В **рабочей области программы** щелкните правой кнопкой мыши библиотеку **iothub\_amqp\_transport**, выберите **Delete** (Удалить) и нажмите кнопку **ОК** для подтверждения.

1. В **рабочей области программы** щелкните правой кнопкой мыши библиотеку **azure\_amqp\_c**, выберите **Delete** (Удалить) и нажмите кнопку **ОК** для подтверждения.

1. В **рабочей области программы** щелкните правой кнопкой мыши проект **remote_monitoring**, выберите **Import Library** (Импортировать библиотеку), а затем — **From URL** (С URL-адреса).
   
    ![Запуск импорта библиотеки в рабочую область mbed][6]

1. Во всплывающем окне введите ссылку на библиотеку транспорта MQTT https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/, а затем нажмите кнопку **Import** (Импорт).
   
    ![Импорт библиотеки в рабочую область mbed][12]

1. Повторите предыдущий шаг, чтобы добавить библиотеку MQTT с URL-адреса https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/.

1. Теперь рабочая область выглядит так:

    ![Просмотр рабочей области mbed][13]

1. Откройте файл remote\_monitoring\remote_monitoring.c и замените имеющиеся инструкции `#include` следующим кодом:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. Удалить весь остальной код из файла remote\_monitoring\remote\_monitoring.c.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Добавьте код для вызова функции **remote\_monitoring\_run**, а затем создайте и запустите приложение устройства.

1. Добавьте функцию **main**, вставив следующий код в конце файла remote\_monitoring.c для вызова функции **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Щелкните **Скомпилировать**, чтобы собрать программу. Вы можете проигнорировать все предупреждения. Однако если при сборке возникают ошибки, исправьте их, прежде чем продолжить.

1. Если сборка выполнена успешно, то на веб-сайте компилятора mbed создается BIN-файл с именем вашего проекта и загружается на локальный компьютер. Скопируйте BIN-файл на устройство. При сохранении BIN-файла на устройство происходит перезапуск устройства и запуск программы, содержащейся в BIN-файле. Программу можно вручную перезапустить в любое время, нажав кнопку сброса на устройстве mbed.

1. Подключитесь к устройству с помощью клиентского SSH-приложения, такого как PuTTY. Вы можете посмотреть, какой последовательный порт использует устройство, в диспетчере устройств Windows.
   
    ![][11]

1. В PuTTY выберите тип подключения **Serial** (Последовательный). Устройство обычно подключается со скоростью 9600 бод, поэтому введите 9600 в поле **Speed** (Скорость). Затем щелкните **Открыть**.

1. Начнется выполнение программы. Если программа не запускается автоматически при подключении, возможно, нужно перезагрузить плату (для этого нажмите клавиши Ctrl+Break или кнопку сброса на плате).
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

