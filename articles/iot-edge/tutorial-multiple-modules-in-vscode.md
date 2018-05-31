---
title: Управление несколькими модулями Azure IoT Edge в VS Code | Документация Майкрософт
description: Использование Visual Studio Code для разработки решений IoT Edge, использующих несколько модулей.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166358"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Разработка решения IoT Edge с несколькими модулями в Visual Studio Code — предварительная версия
Visual Studio Code можно использовать для разработки решения IoT Edge с несколькими модулями. В этой статье приводятся пошаговые инструкции, с помощью которых вы сможете создать, обновить и развернуть решение IoT Edge, которое передает данные от датчиков на имитированное устройство IoT Edge, в Visual Studio Code. 

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить все действия, описанные в этой статье, необходимо следующее:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Расширение C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd) 
- Шаблон AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`).
- активный Центр Интернета вещей с устройством IoT Edge (как минимум).


* Установите [Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) с интеграцией в обозреватель для управления образами и контейнерами.


## <a name="prepare-your-first-iot-edge-solution"></a>Подготовка первого решения IoT Edge
1. В палитре команд VS Code введите и выполните команду **Edge: New IoT Edge solution**. Затем выберите папку рабочей области, введите имя решения (по умолчанию используется имя **EdgeSolution**) и создайте в этом решении первый модуль C# (**SampleModule**). Кроме того, для первого модуля следует указать репозиторий образов Docker. По умолчанию используется репозиторий образов на базе локального реестра Docker (`localhost:5000/<first module name>`). Также вместо него можно использовать реестр контейнеров Azure или Docker Hub.

   > [!NOTE]
   > Если вы используете локальный реестр Docker, обязательно запускайте его с помощью команды `docker run -d -p 5000:5000 --restart=always --name registry registry:2` в окне консоли.

2. Окно VS Code загружает рабочую область решения IoT Edge. Корневая папка содержит папку `modules`, папку `.vscode` и файл шаблона манифеста развертывания. Конфигурации отладки размещаются в папке `.vscode`. Коды всех пользовательских модулей находятся во вложенных папках каталога `modules`. `deployment.template.json` — это шаблон манифеста развертывания. Для некоторых параметров в этом файле значения извлекаются из файла `module.json`, который есть в папке каждого модуля.

3. Добавьте в этот проект решения второй модуль. Для этого введите и выполните команду **Edge: Add IoT Edge module** и выберите файл шаблона развертывания для обновления. Теперь выберите элемент **Azure Function - C#** (Функция Azure — C#) с именем **SampleFunction**, а также соответствующий репозиторий образов Docker.

4. Откройте файл `deployment.template.json` и убедитесь, что он объявляет три модуля в дополнение к среде выполнения. Сообщение создается в модуле `tempSensor` и передается напрямую через `SampleModule` и `SampleFunction`, а затем отправляется в центр Интернета вещей. 
5. Измените маршруты этих модулей, используя приведенное ниже содержимое.
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Сохраните этот файл.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Сборка и развертывание решения IoT Edge
1. В палитре команд VS Code введите и выполните команду **Edge: Build IoT Edge solution**. Используя файлы `module.json` в каждой папке, эта команда выполнит сборку, контейнеризацию и отправку образов Docker для каждого модуля. Затем она передаст обязательное значение для `deployment.template.json` и создаст файл `deployment.json` с информацией из папки `config`. Ход сборки можно отслеживать во встроенном терминале VS Code.

2. В обозревателе устройств Центра Интернета вещей Azure щелкните правой кнопкой мыши идентификатор устройства IoT Edge и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge). Выберите `deployment.json` в папке `config`. Ход развертывания можно отслеживать с помощью идентификатора развертывания во встроенном терминале VS Code.

3. Если вы моделируете устройство IoT Edge на компьютере разработки, то через несколько минут вы увидите, что запущены все контейнеры образов модулей.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Чтобы отслеживать данные, поступающие в Центр Интернета вещей, выберите **Вид** > **Палитра команд** и щелкните **IoT: Start monitoring D2C message** (Интернет вещей: начать мониторинг сообщений D2C). 
2. Чтобы перестать отслеживать данные, используйте команду **IoT: Stop monitoring D2C message** (Центр Интернета вещей: остановить мониторинг сообщений D2C) в палитре команд. 

## <a name="next-steps"></a>Дополнительная информация

Узнайте о других сценариях разработки для Azure IoT Edge в VS Code:

* [Отладка модуля C# в VS Code](how-to-vscode-debug-csharp-module.md)
* [Отладка функции C# в VS Code](how-to-vscode-debug-azure-function.md)