---
title: "Разработка и выполнение Функций Azure локально | Документация Майкрософт"
description: "Узнайте, как программировать и тестировать функции Azure на локальном компьютере перед их запуском в Функциях Azure."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: ru-ru
ms.lasthandoff: 06/09/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Как программировать и тестировать функции Azure в локальной среде

Для локального запуска среды выполнения службы "Функции Azure" можно использовать предпочитаемый редактор кода и средства разработки в локальной среде. Кроме того, можно активировать события в Azure и выполнять отладку кода C# и функций JavaScript.

Чтобы начать, установите [основные инструменты службы "Функции Azure"] из npm. Основные инструменты службы "Функции Azure" являются локальной версией среды выполнения службы "Функции Azure", которые можно запускать на локальном компьютере Windows. Это не эмулятор или симулятор. Это та же среда выполнения, которая используется в Azure.

[основные инструменты службы "Функции Azure"] добавляют следующие псевдонимы команд:
- `func`
- `azfun`
- `azurefunctions`

Основные инструменты службы "Функции Azure" [имеют открытый код и размещаются на GitHub](https://github.com/azure/azure-functions-cli). Чтобы зарегистрировать ошибку или отправить запрос на функцию, [откройте вопрос на GitHub](https://github.com/azure/azure-functions-cli/issues).

## <a name="create-a-local-functions-project"></a>Создание локального проекта службы "Функции"

В локальном режиме работы проект службы "Функции" является каталогом, в котором находятся файлы host.json и local.settings.json. Этот каталог является эквивалентом приложения-функции в Azure. Дополнительные сведения о структуре папок службы "Функции Azure" см. в [этом разделе](functions-reference.md#folder-structure).

В командной строке выполните следующую команду:

```
func init MyFunctionProj
```

Выходные данные выглядят так:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Чтобы отказаться от создания репозитория, используйте параметр `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Файл с локальными параметрами

Файл local.settings.json хранит параметры приложения, строки подключения и параметры основных инструментов службы "Функции Azure". Он имеет следующую структуру:

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

Укажите параметры приложения в коллекции **Значения**. Эти параметры могут читаться, как переменные среды. Для C# используйте `System.Environment.GetEnvironmentVariable` или `ConfigurationManager.AppSettings`. Для JavaScript используйте `process.env`. Если указать один и тот же параметр в качестве переменной среды, он будет иметь приоритет над значениями в файле local.settings.json.

Параметр приложения **AzureWebJobsStorage** — это специальный параметр, который требуется использовать в среде выполнения Функций Azure для всех триггеров, отличных от HTTP. Внутри среда выполнения создает очереди для управления триггерами в этой учетной записи хранения. Если значение параметра **AzureWebJobsStorage** не указано и используются триггеры, отличные от HTTP, отобразится следующее сообщение:

*Missing value for AzureWebJobsStorage in local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json.* (Предупреждение. Не удается найти параметр с именем MyStorageConnection в файле local.settings.json, который соответствует свойству connection, установленному в blobTrigger в файле BlobTriggerCSharp\function.json. Выполните команду func azure functionapp fetch-app-settings или укажите строку подключения в файле local.settings.json.)

> [!NOTE]
> Можно использовать эмулятор локального хранилища с помощью строки подключения "AzureWebJobsStorage": "UseDevelopmentStorage=true". Тем не менее могут обнаружиться различия в поведении по сравнению со службой хранилища Azure.

Следующие параметры настраивают локальный узел службы "Функции":
- `LocalHttpPort`. Порт для `func host start` `func run`, используемый по умолчанию. Параметр командной строки `--port` имеет приоритет над этим значением.
- `CORS`. Разрешенные источники CORS в виде разделенного запятыми списка без пробелов. Используйте "*", чтобы разрешить все.

Строки подключения можно указать в объекте `ConnectionStrings`. Они добавляются в среду с именем поставщика **System.Data.SqlClient**.

Большинство триггеров и привязок имеют свойство **подключение**, которое является именем параметра приложения или переменной среды в файле local.settings.json. Если значение параметра приложения отсутствует, может появиться следующее сообщение:

*Warning: Cannot find value named 'MyStorageConnection' in local.settings.json that matches 'connection' property set on 'blobTrigger' in 'BlobTriggerCSharp\function.json'. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json.* (Предупреждение. Не удается найти параметр с именем MyStorageConnection в файле local.settings.json, который соответствует свойству connection, установленному в blobTrigger в файле BlobTriggerCSharp\function.json. Выполните команду func azure functionapp fetch-app-settings или укажите строку подключения в файле local.settings.json.)

Файл local.settings.json используется только основными инструментами службы "Функции Azure". Чтобы задать параметры приложения и строки подключения в Azure, используйте колонку **Параметры приложения**.

### <a name="configure-app-settings"></a>Настройка параметров приложения

Чтобы задать значение для строки подключения, выполните одно из следующих действий.
- Вручную введите строку подключения из [обозревателя хранилищ Azure](http://storageexplorer.com/).
- Используйте команду **func azure functionapp fetch-app-settings \<FunctionAppName\>**. Требуется **azure login**.
- Используйте **func azure functionapp storage fetch-connection-string \<StorageAccountName\>**. Требуется **azure login**.

## <a name="create-a-function"></a>Создание функции

Чтобы создать функцию, выполните `func new`. Эта команда имеет следующие необязательные аргументы.

- `--language [-l]`. Язык программирования шаблона, например C#, F# или JavaScript.
- `--template [-t]`. Имя шаблона.
- `--name [-n]`. Имя функции.

Например, чтобы создать триггер HTTP на JavaScript, выполните следующую команду:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Чтобы создать функцию, активируемую с помощью очереди, выполните следующую команду:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>Запуск функций в локальной среде

Чтобы запустить проект службы "Функции", запустите узел этой службы. Узел включает триггеры для всех функций в проекте.

```
func host start
```

С `func host start` можно использовать следующие параметры:

- `--port [-p]`. Локальный порт для прослушивания. Значение по умолчанию: 7071.
- `--debug <type>`. Возможные варианты: VSCode и VS.
- `--cors`. Список разрешенных источников CORS, разделенный запятыми без пробелов.
- `--nodeDebugPort [-n]`. Порт отладчика узла. Значение по умолчанию — значение из launch.json или 5858.
- `--debugLevel [-d]`. Уровень трассировки консоли (off, verbose, info, warning или error). Значение по умолчанию — info.
- `--timeout [-t]`. Время ожидания для запуска узла службы "Функции" в секундах. Значение по умолчанию — 20 секунд.
- `--useHttps`. Привязка к https://localhost: {port}, а не к http://localhost: {port}. По умолчанию этот параметр создает доверенный сертификат на компьютере.
- `--pause-on-error`. Приостановка для получения дополнительных входных данных перед выходом из процесса. Это удобно при запуске основных инструментов службы "Функции Azure" из интегрированной среды разработки (IDE).

При запуске узла службы "Функции" выводится URL-адрес функций, активируемых по HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>Отладка

Чтобы подключить отладчик, передайте аргумент `--debug`. Для отладки функций на JavaScript используйте код Visual Studio. Для функций на C# используйте Visual Studio.

Для отладки функции на C# используйте `--debug vs`. Как вариант, используйте [инструменты Visual Studio 2017 для службы "Функции Azure"](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Для запуска узла и настройки отладки JavaScript выполните следующую команду:

```
func host start --debug vscode
```

Затем в коде Visual Studio в представлении **Отладка** выберите **Attach to Azure Functions** (Присоединение к службе "Функции Azure"). Вы можете присоединить точки останова, просмотреть значения переменных и осуществить пошаговое выполнение кода.

![Отладка с помощью Visual Studio 2015](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>Вызов функции с помощью тестовых данных

С помощью `func run <FunctionName>` функцию можно вызвать напрямую. Эта команда аналогична вкладке **тестирования** на портале Azure, где можно ввести входные данные для этой функции. Эта команда запускает весь узел службы "Функции".

С `func run` можно использовать следующие параметры:

- `--content [-c]`. Встроенное содержимое.
- `--debug [-d]`. Подключение отладчика к хост-процессу перед выполнением функции.
- `--timeout [-t]`. Время ожидания (в секундах), пока не будет готов локальный узел службы "Функции".
- `--file [-f]`. Имя файла для использования в качестве содержимого.
- `--no-interactive`. Не запрашивает тип входных данных. Полезно для сценариев автоматизации.

Например, для вызова функции, активируемой по HTTP, и передачи основного содержимого выполните следующую команду:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>Публикация приложения-функции

Чтобы опубликовать проект функций для приложения-функции в Azure, используйте команду `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Можно использовать приведенные ниже параметры.

- `--publish-local-settings [-i]`.  Публикация параметров из файла local.settings.json в Azure с запросом на перезапись, если параметр уже существует.
- `--overwrite-settings [-y]`. Должен использоваться с `-i`. При другом значении перезаписывает локальное значение AppSettings в Azure. Значение по умолчанию — запрос.

Команда `publish` отправляет содержимое в каталог проекта функций. При удалении файлов в локальной среде эта команда не удаляет их из Azure. Чтобы удалить эти файлы на портале Функций Azure, используйте Kudu. Чтобы запустить Kudu, на портале службы "Функции Azure" выберите **Platform Features** (Функции платформы) > **Advanced Tools (Kudu)** (Дополнительные инструменты (Kudu)). 


<!-- LINKS -->

[основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools

