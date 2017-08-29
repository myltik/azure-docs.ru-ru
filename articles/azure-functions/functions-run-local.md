---
title: "Разработка и выполнение Функций Azure локально | Документация Майкрософт"
description: "Узнайте, как программировать и тестировать функции Azure на локальном компьютере перед их запуском в Функциях Azure."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/12/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Как программировать и тестировать функции Azure в локальной среде

Хотя на [портале Azure] имеется полный набор средств для разработки и тестирования Функций Azure, многие разработчики предпочитают локальную среду разработки. Функции Azure позволяют легко использовать любой редактор кода и локальные средства разработки для разработки и тестирования функций на локальном компьютере. Функции могут вызывать события в Azure, а вы можете отлаживать функции C# и JavaScript на локальном компьютере. 

Если вы используете Visual Studio C# для разработки, Функции Azure также [интегрируются с Visual Studio 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Установка основных инструментов Функций Azure

Основные инструменты службы "Функции Azure" являются локальной версией среды выполнения службы "Функции Azure", которые можно запускать на локальном компьютере Windows. Это не эмулятор или симулятор. Это та же среда выполнения, которая используется в Функциях Azure.

[Основные инструменты Функций Azure] предоставляются в виде пакета npm. Сначала необходимо [установить NodeJS](https://docs.npmjs.com/getting-started/installing-node), в состав которого входит npm.  

>[!NOTE]
>В настоящее время пакет основных инструментов Функций Azure можно установить только на компьютерах Windows. Это связано с временным ограничением в узле Функций.

[Основные инструменты Функций Azure] добавляют следующие псевдонимы команд:
* **func**
* **azfun**
* **azurefunctions**

Все эти псевдонимы можно использовать вместо команд `func`, показанных в примерах в этой статье.

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
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>", 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Настройка      | Описание                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Если задано значение **true**, все значения шифруются с помощью ключа локального компьютера. Используется с командами `func settings`. Значение по умолчанию — **false**. |
| **Значения** | Коллекция параметров приложения, используемых при локальном выполнении. Добавьте параметры приложения в этот объект.  |
| **AzureWebJobsStorage** | Задает строку подключения к учетной записи хранения Azure, которая используется в среде выполнения Функций Azure. Эта учетная запись хранения поддерживает триггеры функций. Параметр подключения к учетной записи хранения необходим для всех функций, кроме функций, активируемых протоколом HTTP.  |
| **AzureWebJobsDashboard** | Задает строку подключения к учетной записи хранения Azure, которая используется для хранения журналов функций. Это необязательное значение обеспечивает доступ к журналам на портале.|
| **Host** | Параметры в этом разделе служат для настройки хост-процесса Функций при выполнении в локальной среде. | 
| **LocalHttpPort** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Параметр командной строки `--port` имеет приоритет над этим значением. |
| **CORS** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (**\***), разрешающее запросы из любого источника. |
| **ConnectionStrings** | Содержит строки подключения к базе данных для функций. Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика **System.Data.SqlClient**.  | 

Большинство триггеров и привязок имеют свойство **Connection**, которое сопоставляется с именем переменной среды или параметра приложения. Для каждого свойства подключения должен быть определен параметр в файле local.settings.json. 

Эти параметры также могут считываться в коде как переменные среды. В C# используйте [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) или [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). Для JavaScript используйте `process.env`. Параметры, указанные как системные переменные среды, имеют приоритет над значениями в файле local.settings.json. 

Параметры в файле local.settings.json используются инструментами Функций только при выполнении в локальной среде. По умолчанию эти параметры не переносятся автоматически при публикации проекта в Azure. [При публикации](#publish) используйте параметр `--publish-local-settings`, чтобы добавить эти параметры в приложение-функцию в Azure.

Если для **AzureWebJobsStorage** не задана допустимая строка подключения к хранилищу, выводится следующее сообщение об ошибке:  

>Отсутствует значение AzureWebJobsStorage в local.settings.json. This is required for all triggers other than HTTP. Выполните команду "func azure functionary fetch-app-settings <functionAppName>" или укажите строку подключения в файле local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Настройка параметров приложения

Чтобы задать значение для строки подключения, выполните одно из следующих действий.
* Введите строку подключения из [обозревателя хранилищ Azure](http://storageexplorer.com/).
* Используйте одну из следующих команд:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
    ```
    Обе команды требуют сначала выполнить вход в Azure.

## <a name="create-a-function"></a>Создание функции

Чтобы создать функцию, выполните следующую команду:

```
func new
``` 
`func new` имеет указанные ниже необязательные аргументы.

| Аргумент     | Описание                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Язык программирования шаблона, например C#, F# или JavaScript. |
| **`--template -t`** | Имя шаблона. |
| **`--name -n`** | Имя функции. |

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

`func host start` имеет указанные ниже параметры.

| Параметр     | Описание                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Локальный порт для прослушивания. Значение по умолчанию: 7071. |
| **`--debug <type>`** | Возможные значения: `VSCode` и `VS`. |
| **`--cors`** | Список разрешенных источников CORS, разделенный запятыми без пробелов. |
| **`--nodeDebugPort -n`** | Порт отладчика узла. Значение по умолчанию — значение из launch.json или 5858. |
| **`--debugLevel -d`** | Уровень трассировки консоли (off, verbose, info, warning или error). Значение по умолчанию — info.|
| **`--timeout -t`** | Время ожидания для запуска узла Функций в секундах. Значение по умолчанию — 20 секунд.|
| **`--useHttps`** | Привязка к https://localhost:{port}, а не к http://localhost:{port}. По умолчанию этот параметр создает доверенный сертификат на компьютере.|
| **`--pause-on-error`** | Приостановка для получения дополнительных входных данных перед выходом из процесса. Это удобно при запуске основных инструментов службы "Функции Azure" из интегрированной среды разработки (IDE).|

При запуске узла службы "Функции" выводится URL-адрес функций, активируемых по HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Отладка в VS Code или Visual Studio

Чтобы подключить отладчик, передайте аргумент `--debug`. Для отладки функций на JavaScript используйте код Visual Studio. Для функций на C# используйте Visual Studio.

Для отладки функции на C# используйте `--debug vs`. Кроме того, можно использовать [инструменты Visual Studio 2017 для Функций Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Для запуска узла и настройки отладки JavaScript выполните следующую команду:

```
func host start --debug vscode
```

Затем в коде Visual Studio в представлении **Отладка** выберите **Attach to Azure Functions** (Присоединение к службе "Функции Azure"). Вы можете присоединить точки останова, просмотреть значения переменных и осуществить пошаговое выполнение кода.

![Отладка с помощью Visual Studio 2015](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Передача тестовых данных в функцию

Вы также можете вызвать функцию напрямую с помощью `func run <FunctionName>` и предоставить входные данные для нее. Эта команда аналогична выполнению функции с помощью вкладки **Тест** на портале Azure. Эта команда запускает весь узел службы "Функции".

`func run` имеет указанные ниже параметры.

| Параметр     | Описание                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Встроенное содержимое. |
| **`--debug -d`** | Подключение отладчика к хост-процессу перед выполнением функции.|
| **`--timeout -t`** | Время ожидания (в секундах), пока не будет готов локальный узел службы "Функции".|
| **`--file -f`** | Имя файла для использования в качестве содержимого.|
| **`--no-interactive`** | Не запрашивает тип входных данных. Полезно для сценариев автоматизации.|

Например, для вызова функции, активируемой по HTTP, и передачи основного содержимого выполните следующую команду:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Публикация в Azure

Чтобы опубликовать проект функций для приложения-функции в Azure, используйте команду `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Можно использовать приведенные ниже параметры.

| Параметр     | Описание                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Публикация параметров из файла local.settings.json в Azure с запросом на перезапись, если параметр уже существует.|
| **`--overwrite-settings -y`** | Должен использоваться с `-i`. При другом значении перезаписывает локальное значение AppSettings в Azure. Значение по умолчанию — запрос.|

Команда `publish` отправляет содержимое в каталог проекта функций. При удалении файлов в локальной среде команда `publish` не удаляет их из Azure. Удалить файлы в Azure можно с помощью [средства Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) на [портале Azure].

## <a name="next-steps"></a>Дальнейшие действия

Основные инструменты службы "Функции Azure" [имеют открытый код и размещаются на GitHub](https://github.com/azure/azure-functions-cli).  
Чтобы зарегистрировать ошибку или отправить запрос на функцию, [откройте вопрос на GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Основные инструменты Функций Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[портале Azure]: https://portal.azure.com 

