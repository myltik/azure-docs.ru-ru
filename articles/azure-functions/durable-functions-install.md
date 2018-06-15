---
title: Установка расширения устойчивых функций и примеров в Azure
description: Сведения о том, как установить расширение устойчивых функций для Функций Azure для разработки с помощью портала или Visual Studio.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4dd4bbb9c382b772f8f60b259844e7e471ec73e3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762359"
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Установка расширения устойчивых функций и примеров (Функции Azure)

Расширение [устойчивых функций](durable-functions-overview.md) для Функций Azure предоставляется в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). В этой статье показано, как установить пакет и набор примеров для следующих сред разработки:

* Visual Studio 2017 (рекомендуется для C#); 
* Visual Studio Code (рекомендуется для JavaScript);
* Портал Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Сейчас Visual Studio обеспечивает максимальное удобство разработки приложений с использованием устойчивых функций.  Функции можно выполнять локально, а также публиковать в Azure. Можно начать с пустого проекта или набора примеров функций.

### <a name="prerequisites"></a>предварительным требованиям

* Установите [последнюю версию Visual Studio](https://www.visualstudio.com/downloads/) (15.3 или выше). Включите рабочую нагрузку **разработки Azure** в параметрах установки.

### <a name="start-with-sample-functions"></a>Начало работы с примерами функций 

1. Скачайте [ZIP-файл с примерами приложений для Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Ссылку на NuGet добавлять не нужно, так как она уже содержится в примере проекта.
2. Установите и запустите [эмулятор хранилища Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) версии 5.2 или выше. Вместо этого можно также обновить файл *local.appsettings.json* строки подключения к реальному хранилищу Azure.
3. Откройте проект в Visual Studio 2017. 
4. Инструкции о том, как запустить пример, см. в руководстве по [созданию цепочек функций с примером последовательности приветствия](durable-functions-sequence.md). Пример можно выполнить локально или опубликовать в Azure.

### <a name="start-with-an-empty-project"></a>Начало работы с пустым проектом
 
Следуйте тем же инструкциям, что и в начале работы с примерами, но вместо скачивания *ZIP*-файла выполните следующие действия.

1. Создайте проект приложения-функции.
2. Найдите ссылку на приведенный ниже пакет NuGet, щелкнув *Управление пакетами NuGet*, и добавьте его в проект: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.4.0 (для поиска этого пакета установите флажок *Включить предварительные выпуски*).
   
## <a name="visual-studio-code"></a>Visual Studio Code.

Visual Studio Code поддерживает процесс локальной разработки на всех основных платформах: Windows, macOS и Linux.  Функции можно выполнять локально, а также публиковать в Azure. Можно начать с пустого проекта или набора примеров функций.

### <a name="prerequisites"></a>предварительным требованиям

* Установите [последнюю версию Visual Studio Code](https://code.visualstudio.com/Download). 

* Выполните инструкции раздела "Установка основных инструментов Функций Azure" из статьи [Как программировать и тестировать Функции Azure в локальной среде](https://docs.microsoft.com/azure/azure-functions/functions-run-local).

    >[!IMPORTANT]
    > Если у вас уже есть кроссплатформенные средства Функций Azure, обновите их до последней доступной версии.

    >[!IMPORTANT]
    >Для устойчивых функций в JavaScript требуется версия 2.x основных инструментов решения "Функции Azure".

*  Если вы используете компьютер с Windows, установите и запустите [эмулятор службы хранилища Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) версии 5.2 или выше. Или включите в файл *local.appsettings.json* строку подключения к реальному хранилищу Azure. 


### <a name="start-with-sample-functions"></a>Начало работы с примерами функций

#### <a name="c"></a>C#

1. Клонируйте [репозиторий устойчивых функций](https://github.com/Azure/azure-functions-durable-extension.git).
2. На локальном компьютере перейдите к [папке с примерами скриптов C#](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Установите расширение устойчивых функций Azure, выполнив следующую команду в командной строке или окне терминала:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. Установите расширение Twilio для службы "Функции Azure", выполнив следующую команду в командной строке или окне терминала:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta5
    ```
5. Запустите эмулятор хранения Azure или добавьте в файл *local.appsettings.json* строку подключения к реальному хранилищу Azure.
6. Откройте проект в Visual Studio Code. 
7. Инструкции о том, как запустить пример, см. в руководстве по [созданию цепочек функций с примером последовательности приветствия](durable-functions-sequence.md). Пример можно выполнить локально или опубликовать в Azure.
8. Запустите проект, выполнив в командной строке или окне терминала следующую команду:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (только для службы "Функции" версии 2)

1. Клонируйте [репозиторий устойчивых функций](https://github.com/Azure/azure-functions-durable-extension.git).
2. На локальном компьютере перейдите к [папке с примерами скриптов JavaScript](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Установите расширение устойчивых функций Azure, выполнив следующую команду в командной строке или окне терминала:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. Восстановите пакеты npm, выполнив следующую команду в командной строке или окне терминала:
    
    ```bash
    npm install
    ``` 
5. Обновите файл *local.appsettings.json*, добавив реальную строку подключения к службе хранилища Azure.
6. Откройте проект в Visual Studio Code. 
7. Инструкции о том, как запустить пример, см. в руководстве по [созданию цепочек функций с примером последовательности приветствия](durable-functions-sequence.md). Пример можно выполнить локально или опубликовать в Azure.
8. Запустите проект, выполнив в командной строке или окне терминала следующую команду:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Начало работы с пустым проектом
 
1. В командной строке или окне терминала перейдите к папке, где будет размещаться приложение функции.
2. Установите расширение устойчивых функций Azure, выполнив следующую команду в командной строке или окне терминала:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
3. Создайте приложение функции, выполнив команду ниже.

    ```bash
    func init
    ``` 
4. Запустите эмулятор хранения Azure или добавьте в файл *local.appsettings.json* строку подключения к реальному хранилищу Azure.
5. Затем создайте новую функцию, выполнив следующую команду и все указания мастера.

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > В настоящее время шаблон устойчивых функций недоступен, но вы можете взять за основу любой из поддерживаемых вариантов и внести в его код необходимые изменения. В качестве примера используйте [Клиент оркестрации](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Триггер оркестрации](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence) и [Триггер действия](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Откройте папку проекта в Visual Studio Code и измените код шаблона. 
7. Запустите проект, выполнив в командной строке или окне терминала следующую команду:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Портал Azure

При желании для разработки с использованием устойчивых функций можно использовать портал Azure.

   > [!NOTE]
   > Устойчивые функции в JavaScript пока недоступны на портале.

### <a name="create-an-orchestrator-function"></a>Создание функции оркестратора

1. Создайте приложение-функцию на сайте [functions.azure.com](https://functions.azure.com/signin).

2. Настройте в приложении-функции [использование среды выполнения версии 2.0](set-runtime-version.md).

   Расширение "Устойчивые функции" работает со средами выполнения версий 1.X и 2.0, но шаблоны портала Azure доступны только при нацеливании на среду выполнения версии 2.0.

3. Создайте новую функцию, выбрав действие **Создайте настраиваемую функцию**.

4. Измените **язык** на **C#**, для параметра **Сценарий** установите значение **Устойчивые функции** и выберите шаблон **Durable Functions Http Starter - C#**.

5. В разделе **Расширения не установлены**, нажмите кнопку **Установить**, чтобы скачать расширение с сайта NuGet.org. 

6. Когда установка завершится, переходите к созданию функции клиента оркестрации **HttpStart**, выбрав для этого шаблон **Durable Functions Http Starter - C#**.

7. Теперь создайте функцию оркестрации **HelloSequence** из шаблона **Durable Functions Orchestrator - C#**.

8. И наконец, последняя функция с именем **Hello** создается из шаблона **Durable Functions Activity - C#**.

9. Теперь перейдите к функции **HttpStart** и скопируйте ее URL-адрес.

10. Для вызова устойчивой функции используйте Postman или cURL. Прежде чем переходить к тестированию, замените строку **{functionName}** в URL-адресе именем функции оркестрации (**HelloSequence**).  Не нужно вводить дополнительные данные, просто используйте команду POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Затем вызовите конечную точку **statusQueryGetUri**, чтобы получить текущее состояние устойчивой функции.

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Продолжайте отправлять вызовы к конечной точке **statusQueryGetUri**, пока состояние не изменится на **Завершено**. 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Поздравляем! Ваша устойчивая функция создана и успешно работает на портале Azure!

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Запуск примера создания цепочки функций](durable-functions-sequence.md)
