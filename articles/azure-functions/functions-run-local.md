---
title: Разработка и выполнение Функций Azure локально | Документация Майкрософт
description: Узнайте, как программировать и тестировать функции Azure на локальном компьютере перед их запуском в Функциях Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 523ef25fe0d3227d526acbdee2c7cf2660fc4f25
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Как программировать и тестировать Функции Azure в локальной среде

Хотя на [портале Azure] имеется полный набор средств для разработки и тестирования Функций Azure, многие разработчики предпочитают локальную среду разработки. Функции Azure позволяют легко использовать любой редактор кода и локальные средства разработки для разработки и тестирования функций на локальном компьютере. Функции могут вызывать события в Azure, а вы можете отлаживать функции C# и JavaScript на локальном компьютере. 

Если вы используете Visual Studio C# для разработки, Функции Azure также [интегрируются с Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> Не следует смешивать локальную разработку и разработку с помощью портала разработки в одном приложении-функции. При создании и публикации функций из локального проекта не следует пытаться хранить или изменять код проекта на портале.

## <a name="install-the-azure-functions-core-tools"></a>Установка основных инструментов Функций Azure

[Основные инструменты службы "Функции Azure"] являются локальной версией среды выполнения службы "Функции Azure", которые можно запускать на локальном компьютере для разработки. Это не эмулятор или симулятор. Это та же среда выполнения, которая используется в Функциях Azure. Есть две версии основных инструментов Функций Azure:

+ [Версия 1.x](#v1): поддерживает версию 1.x в среде выполнения. Эта версия поддерживается только на компьютерах с Windows и устанавливается из [пакета npm](https://docs.npmjs.com/getting-started/what-is-npm).
+ [Версия 2.x](#v2): поддерживает версию 2.x в среде выполнения. Эта версия поддерживает [Windows](#windows-npm), [macOS](#brew) и [Linux](#linux). Использует диспетчеры пакетов определенной платформы или пакеты npm для установки. 

### <a name="v1"></a>Версия 1.x

В исходной версии инструментов используется среда выполнения Функций 1.x. Эта версия использует .NET Framework (4.7.1) и поддерживается только на компьютерах с Windows. Прежде чем устанавливать инструменты версии 1.x, необходимо [установить NodeJS](https://docs.npmjs.com/getting-started/installing-node), в состав которого входит пакет npm.

Чтобы установить инструменты версии 1.x, используйте следующую команду:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Версия 2.x

>[!NOTE]
> Среда выполнения Функций Azure версии 2.0 доступна в предварительной версии. Сейчас поддерживаются не все возможности Функций Azure. Дополнительные сведения см. в статье [Обзор версий среды выполнения для решения "Функции Azure"](functions-versions.md). 

В версии 2.x инструментов используется среда выполнения Функций Azure версии 2.x, которая основана на .NET Core. Эта версия поддерживается на всех платформах, которые поддерживает .NET Core 2.x, включая [Windows](#windows-npm), [macOS](#brew) и [Linux](#linux).

#### <a name="windows-npm"></a>Windows

На следующих шагах пакет npm используется для установки основных инструментов на компьютерах с Windows. Кроме того, можно использовать [Chocolatey](https://chocolatey.org/). Дополнительные сведения см. в [файле сведений об основных инструментах](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Установите [.NET Core 2.0 для Windows](https://www.microsoft.com/net/download/windows).

2. Установите [Node.js], который содержит пакет npm. Для версии 2.x этих инструментов поддерживается только версия Node.js 8.5 и более поздние.

3. Установите пакет основных инструментов:

  ```bash
  npm install -g azure-functions-core-tools@core
  ```

#### <a name="brew"></a>MacOS с Homebrew

На следующих шагах Homebrew используется для установки основных инструментов на компьютерах macOS.

1. Установите [.NET Core 2.0 для macOS](https://www.microsoft.com/net/download/macos).

1. Установите [Homebrew](https://brew.sh/), если вы этого не сделали ранее.

2. Установите пакет основных инструментов:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) с APT

На следующих шагах [APT](https://wiki.debian.org/Apt) используется для установки основных инструментов на дистрибутив Linux Ubuntu/Debian. Чтобы выполнить установку на другие дистрибутивы Linux, ознакомьтесь с [файлом сведений об основных инструментах](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Установите [.NET Core 2.0 для Linux](https://www.microsoft.com/net/download/linux).

1. Зарегистрируйте ключ продукта Майкрософт как доверенный:

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
  ```

2.  Настройте веб-канал пакета, заменив `<version>` в команде ниже на соответствующее имя версии из таблицы:

  ```bash
  sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-<version>-prod <version> main" > /etc/apt/sources.list.d/dotnetdev.list'
  sudo apt-get update
  ```

  | Дистрибутив Linux | `<version>` |
  | --------------- | ----------- |
  | Ubuntu 17.10    | `artful`    |
  | Ubuntu 17.04    | `zesty`     |
  | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

3. Установите пакет основных инструментов:

  ```bash
  sudo apt-get install azure-functions-core-tools
  ```

## <a name="run-azure-functions-core-tools"></a>Запуск основных инструментов службы "Функции Azure"
 
Основные инструменты службы "Функции Azure" добавляют следующие псевдонимы команд:
* **func**
* **azfun**
* **azurefunctions**

Любой из этих псевдонимов можно использовать вместо команды `func`, показанной в примерах.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Создание локального проекта службы "Функции"

В локальном режиме работы проект службы "Функции" является каталогом, в котором находятся файлы [host.json](functions-host-json.md) и [local.settings.json](#local-settings-file). Этот каталог является эквивалентом приложения-функции в Azure. Дополнительные сведения о структуре папок службы "Функции Azure" см. в [этом разделе](functions-reference.md#folder-structure).

В окне терминала или из командной строки выполните следующую команду, чтобы создать проект и локальный репозиторий Git:

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

Чтобы создать проект без локального репозитория Git, используйте параметр `--no-source-control [-n]`.

## <a name="register-extensions"></a>Регистрация расширений

В версии 2.х среды выполнения решения "Функции Azure" нужно явно зарегистрировать [расширения привязки](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md), используемые в приложении-функции. 

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Файл с локальными параметрами

Файл local.settings.json хранит параметры приложения, строки подключения и параметры основных инструментов службы "Функции Azure". Он имеет следующую структуру:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
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
| Параметр      | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Если задано значение **true**, все значения шифруются с помощью ключа локального компьютера. Используется с командами `func settings`. Значение по умолчанию — **false**. |
| **Значения** | Коллекция параметров приложения, используемых при локальном выполнении. **AzureWebJobsStorage** и **AzureWebJobsDashboard** являются примерами. Полный список см. в разделе [Справочник по параметрам приложения](functions-app-settings.md). Многие триггеры и привязки имеют свойство, относящееся к параметру приложения, например **Connection** для триггера хранилища BLOB-объектов. Для таких свойств требуется параметр приложения, определенный в массиве **Values**. Это также относится к любому свойству привязки, которое вы задали для имени параметра приложения, поместив значение между знаками процента, например `%AppSettingName%`. |
| **Host** | Параметры в этом разделе служат для настройки хост-процесса Функций при выполнении в локальной среде. | 
| **LocalHttpPort** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Параметр командной строки `--port` имеет приоритет над этим значением. |
| **CORS** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (\*), разрешающее запросы из любого источника. |
| **ConnectionStrings** | Содержит строки подключения к базе данных для функций. Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика **System.Data.SqlClient**.  | 

Эти параметры также могут считываться в коде как переменные среды. Дополнительные сведения см. в разделе о переменных среды в этих справочниках для определенного языка:

+ [Предкомпилированный код C#](functions-dotnet-class-library.md#environment-variables)
+ [Скрипт C# (CSX)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Параметры в файле local.settings.json используются инструментами Функций только при выполнении в локальной среде. По умолчанию эти параметры не переносятся автоматически при публикации проекта в Azure. [При публикации](#publish) используйте параметр `--publish-local-settings`, чтобы добавить эти параметры в приложение-функцию в Azure.

Если для **AzureWebJobsStorage** не задана допустимая строка подключения к хранилищу, выводится следующее сообщение об ошибке:  

>Отсутствует значение AzureWebJobsStorage в local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json (Отсутствует значение AzureWebJobsStorage в local.settings.json. Оно требуется для всех триггеров, отличных от HTTP. Выполните команду func azure functionapp fetch-app-settings или укажите строку подключения в файле local.settings.json).
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Настройка параметров приложения

Чтобы задать значение для строки подключения, выполните одно из следующих действий:
* Введите строку подключения из [обозревателя хранилищ Azure](http://storageexplorer.com/).
* Используйте одну из следующих команд:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Обе команды требуют сначала выполнить вход в Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Создание функции

Чтобы создать функцию, выполните следующую команду:

```
func new
``` 
`func new` имеет указанные ниже необязательные аргументы.

| Аргумент     | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Язык программирования шаблона, например C#, F# или JavaScript. |
| **`--template -t`** | Имя шаблона. |
| **`--name -n`** | Имя функции. |

Например, чтобы создать триггер HTTP на JavaScript, выполните следующую команду:

```
func new --language JavaScript --template "Http Trigger" --name MyHttpTrigger
```

Чтобы создать функцию, активируемую с помощью очереди, выполните следующую команду:

```
func new --language JavaScript --template "Queue Trigger" --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Запуск функций в локальной среде

Чтобы запустить проект службы "Функции", запустите узел этой службы. Узел включает триггеры для всех функций в проекте.

```
func host start
```

`func host start` имеет указанные ниже параметры.

| Параметр     | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Локальный порт для прослушивания. Значение по умолчанию: 7071. |
| **`--debug <type>`** | Возможные значения: `VSCode` и `VS`. |
| **`--cors`** | Список разрешенных источников CORS, разделенный запятыми без пробелов. |
| **`--nodeDebugPort -n`** | Порт отладчика узла. Значение по умолчанию — значение из launch.json или 5858. |
| **`--debugLevel -d`** | Уровень трассировки консоли (off, verbose, info, warning или error). Значение по умолчанию — info.|
| **`--timeout -t`** | Время ожидания для запуска узла службы "Функции" в секундах. Значение по умолчанию — 20 секунд.|
| **`--useHttps`** | Привязка к https://localhost:{port}, а не к http://localhost:{port}. По умолчанию этот параметр создает доверенный сертификат на компьютере.|
| **`--pause-on-error`** | Приостановка для получения дополнительных входных данных перед выходом из процесса. Это удобно при запуске основных инструментов службы "Функции Azure" из интегрированной среды разработки (IDE).|

При запуске узла службы "Функции" выводится URL-адрес функций, активируемых по HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Отладка в VS Code или Visual Studio

Чтобы подключить отладчик, передайте аргумент `--debug`. Для отладки функций на JavaScript используйте код Visual Studio. Для функций на C# используйте Visual Studio.

Для отладки функции на C# используйте `--debug vs`. Кроме того, можно использовать [инструменты Visual Studio 2017 для Функций Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Для запуска узла и настройки отладки JavaScript выполните следующую команду:

```
func host start --debug vscode
```

> [!IMPORTANT]
> Для отладки поддерживается только Node.js 8.x. Node.js 9.x не поддерживается. 

Затем в коде Visual Studio в представлении **Отладка** выберите **Attach to Azure Functions** (Присоединение к службе "Функции Azure"). Вы можете присоединить точки останова, просмотреть значения переменных и осуществить пошаговое выполнение кода.

![Отладка с помощью Visual Studio 2015](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Передача тестовых данных в функцию

Чтобы протестировать функции в локальной среде, [запустите узел службы "Функции"](#start) и вызовите конечные точки на локальном сервере, используя HTTP-запросы. Вызываемая конечная точка зависит от типа функции. 

>[!NOTE]  
> В примерах в этой статье используется инструмент cURL для отправки HTTP-запросов из терминала или командной строки. Вы можете использовать любой инструмент для отправки HTTP-запросов к локальному серверу. Инструмент cURL доступен по умолчанию в системах на основе Linux. При использовании Windows [инструмент cURL](https://curl.haxx.se/) необходимо сначала скачать и установить.

Дополнительные сведения о тестировании функций см. в статье [Методика тестирования кода с помощью Функций Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Функции, активируемые по протоколу HTTP или с помощью веб-перехватчика

Вызовите следующую конечную точку, чтобы запустить в локальной среде функции, активируемые по протоколу HTTP или с помощью веб-перехватчика.

    http://localhost:{port}/api/{function_name}

Используйте то же имя сервера и порт, прослушиваемый узлом службы "Функции". Их можно найти в выходных данных, полученных при запуске узла службы "Функции". Этот URL-адрес можно вызвать с помощью любого метода HTTP с поддержкой триггера. 

Следующая команда cURL активирует функцию быстрого запуска `MyHttpTrigger` из запроса GET с параметром _name_, который передается в строке запроса. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
В следующем примере представлена та же функция, вызываемая из запроса POST с передачей параметра _name_ в тексте запроса:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Запросы GET можно выполнять из браузера, передавая данные в строке запроса. Для всех остальных методов HTTP необходимо использовать cURL, Fiddler, Postman или аналогичный инструмент тестирования HTTP.  

#### <a name="non-http-triggered-functions"></a>Функции, не активируемые по протоколу HTTP
Все виды функций, кроме триггеров HTTP и веб-перехватчиков, можно тестировать в локальной среде путем вызова конечной точки администрирования. Вызов этой конечной точки при помощи запроса HTTP POST на локальном сервере активирует функцию. При необходимости можно передать тестовые данные в среду выполнения в тексте запроса POST. Это аналогично выполнению функции с помощью вкладки **Тест** на портале Azure.  

Вызовите следующую конечную точку администрирования, чтобы активировать функции, отличные от HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Чтобы передать тестовые данные в конечную точку администрирования функции, укажите данные в тексте запроса POST. Текст сообщения должен иметь следующий формат JSON:

```JSON
{
    "input": "<trigger_input>"
}
```` 
Значение `<trigger_input>` содержит данные в формате, ожидаемом функцией. В следующем примере представлен запрос POST к функции `QueueTriggerJS`. В этом случае входные данные представляют собой строку, соответствующую сообщению, которое нужно найти в очереди.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Использование команды `func run` в версии 1.x

>[!IMPORTANT]  
> Команда `func run` не поддерживается в версии 2.x инструментов. Дополнительные сведения см. в статье [Выбор целевых версий среды выполнения Функций Azure](set-runtime-version.md).

Вы также можете вызвать функцию напрямую с помощью `func run <FunctionName>` и предоставить входные данные для нее. Эта команда аналогична выполнению функции с помощью вкладки **Тест** на портале Azure. 

`func run` имеет указанные ниже параметры.

| Параметр     | ОПИСАНИЕ                            |
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

### <a name="viewing-log-files-locally"></a>Просмотр файлов журнала в локальной среде

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Публикация в Azure

Чтобы опубликовать проект функций для приложения-функции в Azure, используйте команду `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Можно использовать приведенные ниже параметры.

| Параметр     | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Публикация параметров из файла local.settings.json в Azure с запросом на перезапись, если параметр уже существует.|
| **`--overwrite-settings -y`** | Должен использоваться с `-i`. При другом значении перезаписывает локальное значение AppSettings в Azure. Значение по умолчанию — запрос.|

Эта команда публикует в существующее приложение-функцию в Azure. Если в подписке не существует `<FunctionAppName>`, то возникает ошибка. Чтобы узнать, как создать приложение-функцию из командной строки или из окна терминала, используя Azure CLI, см. статью [Создание приложения-функции для выполнения без сервера](./scripts/functions-cli-create-serverless.md).

Команда `publish` отправляет содержимое в каталог проекта функций. При удалении файлов в локальной среде команда `publish` не удаляет их из Azure. Удалить файлы в Azure можно с помощью [средства Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) на [портале Azure].  

>[!IMPORTANT]  
> При создании приложения-функции в Azure по умолчанию используется версия 1.x среды выполнения Функций. Чтобы заставить приложение-функцию использовать версию 2.x среды выполнения, добавьте параметр приложения `FUNCTIONS_EXTENSION_VERSION=beta`.  
Используйте следующий код Azure CLI для добавления этого параметра в приложение-функцию: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Дополнительная информация

Основные инструменты службы "Функции Azure" [имеют открытый код и размещаются на GitHub](https://github.com/azure/azure-functions-cli).  
Чтобы зарегистрировать ошибку или отправить запрос на функцию, [откройте вопрос на GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools
[портале Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
