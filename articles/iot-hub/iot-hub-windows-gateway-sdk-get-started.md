<properties
	pageTitle="Приступая к работе с пакетом SDK для шлюза центра IoT | Microsoft Azure"
	description="Пошаговое руководство по работе с пакетом SDK для шлюза IoT Azure под управлением Windows иллюстрирует основные понятия, которые нужно знать при работе с пакетом SDK для шлюза IoT Azure."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# IoT Gateway SDK (beta) - Get started using Windows (Пакет SDK для шлюза IoT (бета-версия): приступая к работе с Windows)

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Сборка примера

Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Windows.

1. Откройте **командную строку разработчика для VS2015**.
2. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk**.
3. Запустите сценарий **tools\\build.cmd**. Этот сценарий создает файл решения Visual Studio, собирает решение и проводит тесты. Решение Visual Studio находится в папке **build** локальной копии репозитория **azure-iot-gateway-sdk**.

## Запуск примера

1. Сценарий **build.cmd** создает папку с именем **build** в локальной копии репозитория. Эта папка содержит два модуля, которые используются в данном примере.

    Сценарий сборки размещает **logger\_hl.dll** в папке **build\\modules\\logger\\Debug** и **hello\_world\_hl.dl** в папке **build\\modules\\hello\_world\\Debug**. Используйте эти пути для настройки значения **module path**, как указано в приведенном ниже файле параметров JSON.

2. Файл **hello\_world\_win.json** в папке **samples/hello\_world/src** представляет собой пример файла параметров JSON для Windows, который можно использовать для запуска примера. В представленном ниже примере параметров JSON предполагается, что вы клонировали репозиторий пакета SDK для шлюза в корневую папку **C:**. Если вы загрузили его в другое место, внесите соответствующие изменения в значения **module path** в файле **samples\\hello\_world\\src\\hello\_world\_win.json**.

3. Для модуля **logger\_hl** в разделе **args** укажите в качестве значения **filename** имя и путь файла, который будет содержать данные журнала.

    Вот пример параметров JSON для Windows, который записывается в файл **log.txt** в корневой папке диска **C:**.

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
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

3. В командной строке перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk**.
4. Выполните следующую команду:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0928_2016-->