---
title: Подключение устройства Windows IoT Core к приложению Azure IoT Central | Документация Майкрософт
description: Вы узнаете, как разработчик устройства может подключить устройство MXChip IoT DevKit к приложению Azure IoT Central.
author: miriambrus
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c36a9798718c37fba889323830b76cf8201785cf
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261905"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Подключение устройства Windows IoT Core к приложению Azure IoT Central

В этой статье описано, каким образом разработчик устройства может подключить устройство Windows IoT Core к приложению Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

1. Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в статье [Создание приложения Azure IoT Central](howto-create-application.md).
2. Устройство под управлением операционной системы Windows 10 IoT Core. В данном пошаговом руководстве мы будем использовать Raspberry Pi.

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Windows IoT Core** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Units  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |

### <a name="settings"></a>Параметры

Числовые параметры

| Отображаемое имя | Имя поля | Units | Число десятичных знаков | Минимальная | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>properties

| type            | Отображаемое имя | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | number    |
| текст            | Расположение     | location   | Недоступно       |

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central добавьте реальное устройство на основе шаблона приложения **Windows IoT Core** и запишите строку подключения к устройству. Дополнительные сведения см. в статье [Добавление реального устройства в приложение Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Подготовка устройства Windows IoT Core

Для настройки устройства Windows IoT Core выполните инструкции из [этого пошагового руководства](https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central добавьте реальное устройство на основе шаблона приложения **Windows IoT Core** и запишите строку подключения к устройству. Дополнительные сведения см. в статье [Добавление реального устройства в приложение Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>Подготовка устройства Windows 10 IoT Core

### <a name="what-youll-need"></a>Необходимые компоненты

Вам необходимо настроить физическое устройство Windows 10 IoT Core. Для этого сначала нужно установить эту ОС на устройстве. Узнайте, как настроить устройство Windows 10 IoT Core, [здесь](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Вам также понадобится клиентское приложение, которое может взаимодействовать с Azure IoT Central. Вы можете создать собственное пользовательское приложение, используя пакет SDK для Azure, развернуть его на устройстве с помощью Visual Studio или скачать [предварительно созданный пример](https://developer.microsoft.com/en-us/windows/iot/samples), развернуть и запустить его на устройстве. 

### <a name="deploying-the-sample-client-application"></a>Развертывание примера клиентского приложения

Чтобы развернуть клиентское приложение из предыдущего шага на устройстве Windows 10 IoT, которое нужно подготовить, сделайте следующее:

**Убедитесь, что строка подключения хранится на устройстве для клиентского приложения, которое нужно использовать**.
* На рабочем столе сохраните строку подключения в текстовом файле с именем connection.string.iothub.
* Скопируйте текстовый файл в папку с документами устройства: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`.

После этого вам понадобится открыть [портал устройств Windows](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal), введя в любом браузере путь http://[IP-адрес_устройства]:8080.

Как показано ниже, вам может понадобиться сделать следующее:
1. Развернуть узел "Приложения" в левой части экрана.
2. Щелкнуть Quick-run samples (Примеры быстрого запуска).
3. Щелкнуть Azure IoT Hub Client (Клиент службы "Центр Интернета вещей Azure").
4. Нажать кнопку Deploy and run (Развернуть и запустить).

![GIF-изображение клиента службы "Центр Интернета вещей Azure" на портале устройств Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

При успешном выполнении приложение запустится на устройстве и будет выглядеть следующим образом:

![Снимок экрана приложения клиента службы "Центр Интернета вещей Azure"](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

В Azure IoT Central вы увидите, как код, выполняющийся в Raspberry Pi, взаимодействует с приложением:

* На странице **Measurements** (Измерения) для реального устройства можно просмотреть данные телеметрии.
* На странице **Свойства** можно просмотреть значение передаваемого свойства "Серийный номер".
* На странице **Параметры** можно изменить различные параметры Raspberry Pi (например, напряжение и скорость вращения вентилятора).

## <a name="download-the-source-code"></a>Скачивание исходного кода

Если вы хотите просмотреть и изменить исходный код для клиентского приложения, его можно скачать [на сайте GitHub](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Если вы планируете изменить код, необходимо следовать инструкциям в файле readme для операционной системы настольных устройств [здесь](https://github.com/Microsoft/Windows-iotcore-samples).

> [!NOTE]
> Если **git** не установлен в среде разработки, его можно скачать здесь: [https://git-scm.com/download](https://git-scm.com/download).
