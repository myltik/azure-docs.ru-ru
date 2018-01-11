---
title: "Использование кода Visual Studio для отладки модуля C# с краем IoT Azure | Документы Microsoft"
description: "Отладка Azure IoT границу в VS Code модуля C#"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 01d321dce439e153b494dfd0de52c100dab78f39
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Использовать кода Visual Studio для отладки C# модуля границу IoT Azure
В этой статье приведены подробные инструкции по использованию [кода Visual Studio](https://code.visualstudio.com/) как основное средство разработки для отладки модулей IoT Edge.

## <a name="prerequisites"></a>Технические условия
Предполагается, что вы используете компьютер или виртуальную машину с компьютера разработчика Windows или Linux. IoT пограничного устройства может быть другое физическое устройство или устройство пограничного IoT, вы можете имитировать на компьютере разработки.

Убедитесь, что у вас есть завершенного следующие учебники перед началом в этом руководстве.
- [Использовать кода Visual Studio для разработки модуля C# с краем IoT Azure](how-to-vscode-develop-csharp-module.md)

После завершения предыдущего учебника, необходимо иметь следующие элементы все будет готово,
- Локальный реестр Docker, запущенного на компьютере разработки. Рекомендуется использовать локальный реестр Docker для прототипов и тестирования назначения.
- `Program.cs` Файл с последней код модуля фильтра.
- Обновленное `deployment.json` файл для модуля датчика и модуль фильтра.
- Среда выполнения Edge, запущенного на компьютере разработки.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Сборка модуля IoT Edge для цели отладки
1. Чтобы начать отладку, необходимо использовать **dockerfile.debug** перестроения образа docker и повторно развернуть решение Edge. В обозревателе VS Code щелкните папку Docker, чтобы открыть его. Нажмите кнопку `linux-x64` папку, щелкните правой кнопкой мыши **Dockerfile.debug**и нажмите кнопку **образа Docker модуля Edge построения IoT**.
3. В окне **Выбор папки** перейдите к папке или введите путь `./bin/Debug/netcoreapp2.0/publish`. Щелкните **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR).
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filtermodule:latest`. Если развертывается в локальном реестре, он должен быть `localhost:5000/filtermodule:latest`.
5. Отправьте образ в репозиторий Docker. Используйте **Edge: образа Docker модуля Push IoT Edge** команд и введите URL-адрес изображения в поле всплывающие подсказки в верхней части окна VS Code. Используйте же URL-адрес изображения использовался выше шаг.
6. Можно повторно использовать `deployment.json` для повторного развертывания. В команде палитры, введите и выберите **Edge: перезапустите Edge** для получения модуля фильтра, выполнив в отладочной версии.

## <a name="start-debugging-in-vs-code"></a>Начать отладку в VS Code
1. Перейдите в окно отладчика VS Code. Нажмите клавишу **F5** и выберите **IoT Edge(.Net Core)**
2. В `launch.json`, перейдите к **отладки IoT Edge настраиваемый модуль (.NET Core)** статьи и заполните `<container_name>`под `pipeArgs`. Он должен быть `filtermodule` в этом учебнике.
3. Перейдите в файл Program.cs. Добавьте точку останова в `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Нажмите клавишу **F5** еще раз. И выберите процесс для присоединения. В этом учебнике следует имя процесса`FilterModule.dll`
5. В окне отладки кода VS можно просмотреть переменные в левой панели. 

> [!NOTE]
> Предыдущий пример показывает способы отладки .net Core IoT Edge модулей на контейнеры. Он основан на отладочной версии `Dockerfile.debug`, включая VSDBG (.NET Core отладчик командной строки) при его создании в вашем образе контейнера. Мы рекомендуем непосредственно использовать или настраивать `Dockerfile` без VSDBG для модулей IoT край рабочей среде после окончания отладки модули C#.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике создан модуль IoT Edge и развернуть его для целей отладки и начата отладка его в VS Code. Можно перейти к любой из следующих учебников, чтобы узнать о других сценариях при разработке Azure IoT край VS Code. 

> [!div class="nextstepaction"]
> [Разработка и развертывание модуля C# в VS Code](how-to-vscode-develop-csharp-module.md)
