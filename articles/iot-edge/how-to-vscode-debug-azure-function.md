---
title: "Использование Visual Studio Code для отладки решения \"Функции Azure\" с помощью Azure IoT Edge | Документация Майкрософт"
description: "Отладка функции Azure на языке C# с помощью Azure IoT Edge в VS Code"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Использование Visual Studio Code для отладки решения "Функции Azure" с помощью Azure IoT Edge

В этой статье содержатся подробные инструкции по использованию [Visual Studio Code](https://code.visualstudio.com/) в качестве основного инструмента разработки для отладки решения "Функции Azure" в IoT Edge.

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве предполагается, что в качестве компьютера для разработки вы используете компьютер или виртуальную машину под управлением Windows или Linux. Устройством Azure IoT Edge может быть другое физическое устройство или имитация устройства, запущенная на компьютере разработки.

Перед началом работы обязательно ознакомьтесь со следующими руководствами.
- [Использование Visual Studio Code для разработки и развертывания решения "Функции Azure" в Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Когда вы выполните предыдущее руководство, вы подготовите следующие элементы:
- Локальный реестр Docker, запущенный на компьютере разработки. Рекомендуем использовать локальный реестр Docker для создания прототипов и тестирования.
- Файл `run.csx` со свежей версией кода для функции фильтра.
- Обновленный файл `deployment.json` для модуля датчика и модуля функции фильтра.
- Среда выполнения Edge, выполняемая на компьютере разработки.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Сборка модуля IoT Edge для целей отладки
1. Чтобы начать отладку, повторно выполните сборку образа Docker с помощью **dockerfile.debug** и повторно разверните решение Edge. В проводнике VS Code откройте папку Docker. Затем щелкните папку `linux-x64`, щелкните правой кнопкой мыши файл **Dockerfile.debug** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge).

    ![Сборка образа для отладки](./media/how-to-debug-csharp-function/build-debug-image.png)

2. В окне **Выбор папки** перейдите к проекту **FilterFunction** и щелкните **Select Folder as EXE_DIR** (Выбрать папку в качестве каталога выполнения).
3. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filterfunction:latest`. Если развертывание выполняется в локальном реестре, необходимо использовать `localhost:5000/filterfunction:latest`.

    ![Отправка образа](./media/how-to-debug-csharp-function/push-image.png)

4. Отправьте образ в репозиторий Docker. Запустите команду **Edge: Push IoT Edge module Docker image** (Edge: передать образ Docker для модуля IoT Edge) и укажите URL-адрес образа во всплывающем текстовом поле в верхней части окна VS Code. Используйте тот же URL-адрес образа, что и на предыдущем шаге.
5. Вы можете многократно использовать `deployment.json` для повторного развертывания. На палитре команд введите и выберите **Edge: Restart Edge** (Edge: перезапустить Edge), чтобы запустить модуль фильтра в версии отладки.

## <a name="start-debugging-in-vs-code"></a>Начало отладки в VS Code
1. Перейдите в окно отладки VS Code. Нажмите клавишу **F5** и выберите **IoT Edge(.Net Core)**

    ![Нажатие клавиши F5](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. В файле `launch.json` перейдите к разделу **отладки функции IoT Edge (.NET Core)** и укажите `<container_name>` в разделе `pipeArgs`. Для этого руководства используйте значение `filterfunction`.

    ![Обновление файла launch.json](./media/how-to-debug-csharp-function/update-launch-json.png)

3. Перейдите к файлу run.csx. Добавьте в функцию точку останова.
4. Перейдите в окно отладки (сочетание клавиш Ctrl + Shift + D) и выберите **Отладка функции IoT Edge (.NET Core)** из раскрывающегося списка. 

    ![Выбор режима отладки](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. Нажмите кнопку "Начать отладку" или клавишу **F5**, затем выберите процесс для присоединения.

    ![Процесс присоединения функции](./media/how-to-debug-csharp-function/attach-function-process.png)

6. В левой панели окна отладки VS Code вы можете просматривать переменные. 

> [!NOTE]
> В предыдущем примере показаны процесс отладки функции .Net Core IoT Edge в контейнерах. Он основан на отладочной версии файла `Dockerfile.debug`, который при сборке образа контейнера включает в него отладчик командной строки .NET Core (VSDBG). Мы рекомендуем использовать для рабочей версии функции IoT Edge готовый или отдельно настроенный файл `Dockerfile` без отладчика VSDBG, когда вы завершите отладку функции C#.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль Функций Azure и развернули его в IoT Edge для отладки, а также начали отладку в VS Code. Вы можете перейти к изучению любого из следующих руководств, чтобы узнать о других сценариях при разработке Azure IoT Edge в VS Code. 

> [!div class="nextstepaction"]
> [Разработка и развертывание модуля C# в VS Code](how-to-vscode-develop-csharp-module.md)

