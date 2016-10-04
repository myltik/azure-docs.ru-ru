<properties
	pageTitle="Приступая к работе с пакетом SDK для шлюза центра IoT | Microsoft Azure"
	description="Пошаговое руководство по работе с пакетом SDK для шлюза IoT Azure под управлением Linux иллюстрирует основные понятия, которые нужно знать при работе с пакетом SDK для шлюза IoT Azure."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# IoT Gateway SDK (beta) - Get started using Linux (Пакет SDK для шлюза IoT (бета-версия): приступая к работе с Linux)

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Сборка примера

Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Linux.

1. Откройте оболочку.
2. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk**.
3. Запустите сценарий **tools/build.sh**. В этом сценарии используется утилита **cmake**, которая создает папку **build** в корневой папке локальной копии репозитория **azure-iot-gateway-sdk** и генерирует сборочный файл. Затем сценарий собирает решение и проводит тесты.

> [AZURE.NOTE]  При каждом запуске сценарий **build.sh** удаляет и заново создает папку **build** в корневой папке локальной копии репозитория **azure-iot-gateway-sdk**.

## Запуск примера

1. Сценарий **Build.sh** создает выходные данные в папке **build** локальной копии репозитория **azure-iot-gateway-sdk**. Он включает два модуля, которые используются в данном примере.

    Сценарий сборки размещает **liblogger\_hl.so** в папке **build/modules/logger/** и **libhello\_world\_hl.so** в папке **build/modules/hello\_world/**. Используйте эти пути для настройки значения **module path**, как указано в приведенном ниже файле параметров JSON.

2. Файл **hello\_world\_lin.json** в папке **samples/hello\_world/src** представляет собой пример файла параметров JSON, который можно использовать для запуска примера. В представленном ниже примере параметров JSON предполагается, что пример запускается из корневой папки локальной копии репозитория **azure-iot-gateway-sdk**.

3. Для модуля **logger\_hl** в разделе **args** укажите в качестве значения **filename** имя и путь файла, который будет содержать данные журнала.

    Вот пример параметров JSON для Linux, который записывается в файл **log.txt** в папке, из которой запускается этот пример.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. Перейдите к папке **azure-iot-gateway-sdk** в оболочке.
4. Выполните следующую команду:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0928_2016-->