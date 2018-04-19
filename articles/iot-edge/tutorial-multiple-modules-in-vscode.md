---
title: Работа с несколькими модулями IoT Edge в Visual Studio Code | Документация Майкрософт
description: Развертывание службы "Машинное обучение Azure" в качестве модуля на пограничном устройстве
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Разработка решения IoT Edge с несколькими модулями в Visual Studio Code — предварительная версия
Visual Studio Code можно использовать для разработки решения IoT Edge с несколькими модулями. В этой статье приводятся пошаговые инструкции, с помощью которых вы сможете создать, обновить и развернуть решение IoT Edge, которое передает данные от датчиков на имитированное устройство IoT Edge, в Visual Studio Code. В этой статье раскрываются следующие темы:

* создание решения IoT Edge с помощью Visual Studio Code;
* добавление нового модуля в рабочее решение IoT Edge с помощью Visual Studio Code; 
* развертывание решения IoT Edge (с несколькими модулями) на устройстве IoT Edge;
* Просмотр сформированных данных

## <a name="prerequisites"></a>предварительным требованиям
* Изучите руководства, посвященные следующим темам:
  * [развертывание модуля C#](tutorial-csharp-module.md);
  * [развертывание функции C#](tutorial-deploy-function.md);
  * [развертывание модуля Python](tutorial-python-module.md).
* Установите [Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) с интеграцией в обозреватель для управления образами и контейнерами.


## <a name="prepare-your-first-iot-edge-solution"></a>Подготовка первого решения IoT Edge
1. В палитре команд VS Code введите и выполните команду **Edge: New IoT Edge solution**. Затем выберите папку рабочей области, введите имя решения (по умолчанию используется имя **EdgeSolution**) и создайте в этом решении первый модуль C# (**SampleModule**). Кроме того, для первого модуля следует указать репозиторий образов Docker. По умолчанию используется репозиторий образов на базе локального реестра Docker (`localhost:5000/<first module name>`). Также вместо него можно использовать реестр контейнеров Azure или Docker Hub.

> [!NOTE]
> Если вы используете локальный реестр Docker, обязательно запускайте его с помощью команды `docker run -d -p 5000:5000 --restart=always --name registry registry:2` в окне консоли.

2. Окно VS Code загрузит рабочую область решения IoT Edge. В ней вы найдете папку `modules`, папку `.vscode` и файл шаблона манифеста развертывания в корневой папке. Конфигурации отладки размещаются в папке `.vscode`. Коды всех пользовательских модулей находятся во вложенных папках каталога `modules`. `deployment.template.json` — это шаблон манифеста развертывания. Для некоторых параметров в этом файле значения извлекаются из файла `module.json`, который есть в папке каждого модуля.

3. Добавьте в этот проект решения второй модуль. Для этого введите и выполните команду **Edge: Add IoT Edge module** и выберите файл шаблона развертывания для обновления. Теперь выберите элемент **Azure Function - C#** (Функция Azure — C#) с именем **SampleFunction**, а также соответствующий репозиторий образов Docker, чтобы добавить их в проект.

4. Подготовка первого решения IoT Edge с двумя простыми модулями завершена. Используемый по умолчанию модуль C# выполняет роль модуля сообщений канала, а функция C# предназначена для обработки сообщений канала. В `deployment.template.json` это решение отображается с тремя модулями. Сообщение создается в модуле `tempSensor` и передается напрямую через `SampleModule` и `SampleFunction`, а затем отправляется в центр Интернета вещей. Измените маршруты этих модулей, используя приведенные ниже данные. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Сохраните этот файл.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Сборка и развертывание решения IoT Edge
1. В палитре команд VS Code введите и выполните команду **Edge: Build IoT Edge solution**. Эта команда проверит все модули, используя файлы `module.json` в каждой папке, а затем выполнит сборку, контейнеризацию и отправку образов Docker для каждого модуля. Затем она проанализирует необходимое значение в `deployment.template.json` и создаст `deployment.json` с фактическим значением в папке `config`. Ход сборки можно отслеживать во встроенном терминале VS Code.

2. В обозревателе устройств Центра Интернета вещей Azure щелкните правой кнопкой мыши идентификатор устройства IoT Edge и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge). Выберите `deployment.json` в папке `config`. Ход развертывания можно отслеживать с помощью идентификатора развертывания во встроенном терминале VS Code.

3. Если вы используете [имитированное устройство IoT Edge](tutorial-simulate-device-linux.md) на компьютере разработки, через несколько минут вы увидите, что запущены все контейнеры образов модулей.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Чтобы отслеживать данные, поступающие в Центр Интернета вещей, выберите **Вид** > **Палитра команд** и щелкните **IoT: Start monitoring D2C message** (Интернет вещей: начать мониторинг сообщений D2C). 
2. Чтобы перестать отслеживать данные, используйте команду **IoT: Stop monitoring D2C message** (Центр Интернета вещей: остановить мониторинг сообщений D2C) в палитре команд. 

## <a name="next-steps"></a>Дополнительная информация

Теперь вы можете перейти к изучению любой из указанных ниже статей, чтобы узнать о других сценариях при разработке Azure IoT Edge в Visual Studio Code:

* [Отладка модуля C# в VS Code](how-to-vscode-debug-csharp-module.md)
* [Отладка функции C# в VS Code](how-to-vscode-debug-azure-function.md)