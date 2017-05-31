---
title: "Преобразование данных в шлюзе Интернета вещей с помощью Edge Интернета вещей Azure | Документация Майкрософт"
description: "Шлюз Интернета вещей можно использовать для преобразования формата данных датчиков с помощью настраиваемого модуля из Edge Интернета вещей Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "преобразование данных в шлюзе Интернета вещей, преобразование данных в шлюзе IoT"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7bf9e64db91cb0fec37ff242bea94dbbd0833054
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Использование шлюза Интернета вещей для преобразования данных датчиков с помощью Edge Интернета вещей

> [!NOTE]
> Прежде чем приступить к работе с данным руководством, последовательно пройдите следующие уроки:
> * [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Использование шлюза Интернета вещей для подключения объектов к облаку (подключение SensorTag к Центру Интернета вещей Azure)](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Одна из задач шлюза Интернета вещей — обработка собранных данных перед их отправкой в облако. Edge Интернета вещей Azure позволяет создавать и собирать модули для формирования рабочего процесса обработки данных. Модуль получает сообщение, выполняет с ним определенное действие, а затем передает его для обработки другими модулями.

## <a name="what-you-learn"></a>Что вы узнаете

Вы узнаете, как создать модуль для преобразования сообщений из SensorTag в другой формат.

## <a name="what-you-do"></a>В рамках этого руководства мы:

* Создадим модуль для преобразования полученного сообщения в формат JSON.
* Скомпилируем модуль.
* Добавим модуль в пример приложения BLE из Edge Интернета вещей Azure.
* Запустим пример приложения.

## <a name="what-you-need"></a>Необходимые элементы

* Следующие учебники пройдены в указанном порядке:
  * [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Использование шлюза Интернета вещей для подключения объектов к облаку (подключение SensorTag к Центру Интернета вещей Azure)](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* Клиент SSH, который выполняется на главном компьютере. В Windows рекомендуется использовать PuTTY. Клиент SSH изначально входит в состав ОС Linux и Mac OS.
* IP-адрес, имя пользователя и пароль для доступа к шлюзу из клиента SSH.
* Подключение к Интернету.

## <a name="create-a-module"></a>Создание модуля

1. На главном компьютере запустите клиент SSH и установите подключение к шлюзу Интернета вещей.
1. Создайте клоны исходных файлов модуля преобразования из GitHub в домашнем каталоге шлюза Интернета вещей, выполнив следующие команды:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Это собственный модуль Edge Azure, написанный на языке программирования C. Модуль преобразует формат полученных сообщений в следующий:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>Компиляция модуля

Чтобы скомпилировать модуль, выполните следующие команды:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$\/\/" build.sh
# run the build shell script
./build.sh
```

После завершения компиляции вы получите файл `libmy_module.so`. Запишите абсолютный путь к этому файлу.

## <a name="add-the-module-to-the-ble-sample-application"></a>Добавление модуля к примеру приложения BLE

1. Перейдите в папку примеров, выполнив следующую команду:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Откройте файл конфигурации, выполнив такую команду:

   ```bash
   vi ble_gateway.json
   ```

1. Добавьте модуль, вставив в раздел `modules` следующий код:

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Замените в коде `[Your libmy_module.so path]` на абсолютный путь к файлу libmy_module.so`.
1. Замените код в разделе `links` следующим:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Нажмите клавишу `ESC`, а затем введите `:wq`, чтобы сохранить файл.

## <a name="run-the-sample-application"></a>Запуск примера приложения

1. Включите SensorTag.
1. Задайте значение переменной среды SSL_CERT_FILE, выполнив следующую команду:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Запустите пример приложения с добавленным модулем, выполнив следующую команду:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно преобразовали сообщение из SensorTag в формат JSON с помощью шлюза Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
