---
title: "Использование Visual Studio Code для отладки модуля C# с помощью Azure IoT Edge | Документация Майкрософт"
description: "Отладка модуля C# с помощью Azure IoT Edge в VS Code"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1ab67cd8aaf59cde3157fcb877ce13f10cb432bb
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Использование Visual Studio Code для отладки модуля C# с помощью Azure IoT Edge
В этой статье приведены подробные инструкции по использованию [Visual Studio Code](https://code.visualstudio.com/) в качестве основного средства для отладки модулей IoT Edge.

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве предполагается, что в качестве компьютера для разработки вы используете компьютер или виртуальную машину под управлением Windows или Linux. Устройством Azure IoT Edge может быть другое физическое устройство или же его можно сымитировать на компьютере разработки.

Перед началом работы с этим руководством убедитесь, что вы ознакомились со следующим руководством.
- [Использование Visual Studio Code для разработки модуля C# с помощью Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

После завершения предыдущего руководства необходимо подготовить следующие элементы:
- Локальный реестр Docker, запущенный на компьютере разработки. Рекомендуется использовать локальный реестр Docker для создания прототипов и тестирования.
- Файл `Program.cs` с последним кодом модуля фильтра.
- Обновленный файл `deployment.json` для модулей датчика и фильтра.
- Среда выполнения Edge, выполняемая на компьютере разработки.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Сборка модуля IoT Edge для выполнения отладки
1. Для начала отладки необходимо использовать **dockerfile.debug**, чтобы повторно создать образ docker и развернуть решение Edge. В проводнике VS Code откройте папку Docker. Затем щелкните папку `linux-x64`, щелкните правой кнопкой мыши файл **Dockerfile.debug** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge).

    ![Создание образа для отладки](./media/how-to-debug-csharp-module/build-debug-image.png)

3. В окне **Выбор папки** перейдите к папке или введите путь `./bin/Debug/netcoreapp2.0/publish`. Щелкните **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR).
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filtermodule:latest`. Если развертывание выполняется в локальном реестре, необходимо использовать `localhost:5000/filtermodule:latest`.
5. Отправьте образ в репозиторий Docker. Используйте команду **Edge: Push IoT Edge module Docker image** (Edge: передать образ Docker для модуля IoT Edge) и введите URL-адрес образа во всплывающее текстовое поле в верхней части окна VS Code. Используйте URL-адрес образа, применяемый на предыдущем шаге.
6. Таким образом вы сможете многократно использовать `deployment.json` для повторного развертывания. В палитре команд введите и выберите **Edge: Restart Edge** (Edge: перезапустить Edge), чтобы получить модуль фильтра, выполняющийся в версии отладки.

## <a name="start-debugging-in-vs-code"></a>Начало отладки в VS Code
1. Перейдите в окно отладки VS Code. Нажмите клавишу **F5** и выберите **IoT Edge(.Net Core)**

    ![Нажатие клавиши F5](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. В файле `launch.json` перейдите к разделу **отладки пользовательского модуля IoT Edge (.NET Core)** и укажите `<container_name>` в разделе `pipeArgs`. В этом руководстве это — `filtermodule`.

    ![Изменение pipeArgs](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Перейдите к файлу Program.cs. Добавьте точку останова в `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Нажмите клавишу **F5** еще раз. Выберите процесс для присоединения. В этом руководстве именем процесса должно быть `FilterModule.dll`

    ![Процесс присоединения](./media/how-to-debug-csharp-module/attach-process.png)

5. В окне отладки VS Code переменные можно просмотреть в левой панели. 

> [!NOTE]
> В предыдущем примере показаны способы отладки модулей в контейнерах .Net Core IoT Edge. Он основан на отладочной версии файла `Dockerfile.debug`, включающем в себя VSDBG (отладчик командной строки.NET Core) в вашем образе контейнера при его создании. Рекомендуется непосредственно использовать или настраивать файл `Dockerfile` без VSDBG для готовых модулей IoT Edge после отладки модулей C#.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge и развернули его для отладки, а также начали его отладку в VS Code. Теперь вы можете перейти к изучению любого из следующих руководств, чтобы узнать о других сценариях при разработке Azure IoT Edge в VS Code. 

> [!div class="nextstepaction"]
> [Использование Visual Studio Code для разработки модуля C# с помощью Azure IoT Edge](how-to-vscode-develop-csharp-module.md)
