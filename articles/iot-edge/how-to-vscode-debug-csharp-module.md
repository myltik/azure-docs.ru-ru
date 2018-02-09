---
title: "Использование Visual Studio Code для отладки модуля C# с помощью Azure IoT Edge | Документация Майкрософт"
description: "Отладка модуля C# с помощью Azure IoT Edge в Visual Studio Code."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 46d9ca0bf6c9ddf95c147fc2eb62d275c973845e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Использование Visual Studio Code для отладки модуля C# с помощью Azure IoT Edge
В этой статье приведены подробные инструкции по использованию [Visual Studio Code](https://code.visualstudio.com/) в качестве основного средства для отладки модулей Azure IoT Edge.

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве предполагается, что в качестве компьютера для разработки вы используете компьютер или виртуальную машину под управлением Windows или Linux. Устройством IoT Edge может быть другое физическое устройство или имитация устройства, запущенная на компьютере разработки.

Перед началом работы необходимо выполнить инструкции из такого руководства:
- [Использование Visual Studio Code для разработки модуля C# с помощью Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

Когда вы выполните предыдущее руководство, вы подготовите следующие элементы:
- Локальный реестр Docker, запущенный на компьютере разработки. Это необходимо для создания прототипов и тестирования.
- Файл `Program.cs` с последним кодом модуля фильтра.
- Обновленный файл `deployment.json` для модулей датчика и фильтра.
- Среда выполнения IoT Edge, выполняемая на компьютере разработки.

## <a name="build-your-iot-edge-module-for-debugging"></a>Сборка модуля IoT Edge для отладки
1. Чтобы начать отладку, повторно выполните сборку образа Docker с помощью **dockerfile.debug** и повторно разверните решение IoT Edge. Чтобы открыть папку Docker в обозревателе Visual Studio Code, выберите ее. Затем выберите папку **linux-x64**, щелкните правой кнопкой мыши файл **Dockerfile.debug** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge).

    ![Снимок экрана обозревателя VS Code](./media/how-to-debug-csharp-module/build-debug-image.png)

3. В окне **Выбор папки** перейдите к папке или введите путь **./bin/Debug/netcoreapp2.0/publish**. Затем выберите пункт **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR).
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filtermodule:latest`. Если развертывание выполняется в локальном реестре, необходимо использовать `localhost:5000/filtermodule:latest`.
5. Отправьте образ в репозиторий Docker. Запустите команду **Edge: Push IoT Edge module Docker image** (Edge: передать образ Docker для модуля IoT Edge) и укажите URL-адрес образа во всплывающем текстовом поле в верхней части окна VS Code. Используйте тот же URL-адрес образа, который вы использовали на предыдущем шаге.
6. Вы можете многократно использовать `deployment.json` для повторного развертывания. В палитре команд введите и выберите **Edge: Restart Edge** (Edge: перезапустить Edge), чтобы получить модуль фильтра, выполняющийся в версии отладки.

## <a name="start-debugging-in-vs-code"></a>Начало отладки в VS Code
1. Перейдите в окно отладки VS Code. Нажмите клавишу **F5** и выберите **IoT Edge(.Net Core)**.

    ![Снимок экрана окна отладки VS Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. В файле `launch.json` перейдите к разделу **Debug IoT Edge Custom Module (.NET Core)** (Отладка пользовательского модуля IoT Edge (.NET Core)). В разделе **pipeArgs** укажите `<container_name>`. В этом руководстве его значение должно соответствовать значению `filtermodule`.

    ![Снимок экрана окна отладки VS Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Перейдите к файлу **Program.cs**. Добавьте точку останова в `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Снова нажмите клавишу **F5** и выберите процесс для присоединения. В этом руководстве именем процесса должно быть `FilterModule.dll`.

    ![Снимок экрана окна отладки VS Code](./media/how-to-debug-csharp-module/attach-process.png)

5. На левой панели окна отладки VS Code вы можете просматривать переменные. 

> [!NOTE]
> В предыдущем примере показаны способы отладки модулей в контейнерах .NET Core IoT Edge. Они основаны на отладочной версии файла `Dockerfile.debug`, который при сборке образа контейнера включает в него отладчик командной строки .NET Core (VSDBG). После отладки модулей C# рекомендуется непосредственно использовать или настраивать файл `Dockerfile` без VSDBG для готовых модулей IoT Edge.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge и развернули его для отладки. Вы начали делать его отладку в VS Code. Чтобы узнать о других сценариях при разработке Azure IoT Edge в VS Code, см. статью: 

> [!div class="nextstepaction"]
> [Разработка и развертывание модуля C# в VS Code](how-to-vscode-develop-csharp-module.md)
