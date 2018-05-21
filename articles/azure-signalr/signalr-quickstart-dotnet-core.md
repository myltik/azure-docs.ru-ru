---
title: Краткое руководство по использованию службы Azure SignalR | Документация Майкрософт
description: Краткое руководство по использованию службы Azure SignalR для создания комнаты чата с помощью приложений MVC для ASP.NET Core.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: 78e164b566194fcfe952e3ad59dd3d228f90d193
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-chat-room-with-signalr-service"></a>Краткое руководство по созданию чата с помощью службы SignalR

Azure SignalR — это служба Azure, которая помогает разработчикам без труда создавать веб-приложения с функциями в режиме реального времени. Эта служба работает на основе [SignalR для ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

В этой статье показано, как приступить к работе со службой Azure SignalR. В этом кратком руководстве вы создадите приложение чата с помощью веб-приложения MVC для ASP.NET Core. Это приложение установит соединение с ресурсом службы Azure SignalR для включения обновлений содержимого в режиме реального времени. Вы разместите веб-приложение локально и подключитесь к нескольким клиентам браузера. Каждый клиент сможет принудительно отправлять обновления содержимого остальным клиентам. 


Вы можете использовать любой редактор кода для выполнения шагов в этом руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

Код для этого руководства доступен для скачивания в [репозитории примеров AzureSignalR на GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom).  Кроме того, ресурсы Azure, используемые в этом кратком руководстве, можно создать, следуя инструкциям в статье [Create a SignalR Service](scripts/signalr-cli-create-service.md) (Создание службы SignalR).

![Полное руководство для локальной среды](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>предварительным требованиям

* Установленный [пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows).
* Скачанный или клонированный репозиторий [примеров AzureSignalR](https://github.com/aspnet/AzureSignalR-samples) на Github. 

## <a name="create-an-azure-signalr-resource"></a>Создание ресурса Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

В этом разделе используется [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) для создания проекта веб-приложения MVC для ASP.NET Core. Преимущество использования .NET Core CLI по сравнению с Visual Studio заключается в том, что он доступен на платформах Windows, MacOS и Linux. 

1. Создайте новый каталог для своего проекта В этом кратком руководстве используется папка *E:\Testing\chattest*.

2. В новой папке выполните следующую команду, чтобы создать новый проект веб-приложения MVC для ASP.NET Core.

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Добавление менеджера секретных ключей в проект

В этом разделе добавьте [инструмент "Диспетчер секретов"](https://docs.microsoft.com/aspnet/core/security/app-secrets) в проект. Инструмент "Диспетчер секретов" хранит конфиденциальные данные для разработки вне вашего дерева проектов. Этот подход помогает предотвратить случайный обмен секретами приложений в исходном коде.

1. Откройте файл с расширением *CSPROJ*. Добавьте элемент `DotNetCliToolReference`, чтобы включить *Microsoft.Extensions.SecretManager.Tools*. Кроме того, добавьте элемент `UserSecretsId`, как показано ниже, и сохраните файл.

    *chattest.csproj:*

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Добавление веб-приложения Azure SignalR

1. Добавьте ссылку на пакет NuGet `Microsoft.Azure.SignalR`, выполнив следующую команду:

        dotnet add package Microsoft.Azure.SignalR -v 1.0.0-preview-10007

2. Выполните следующую команду, чтобы восстановить пакеты проекта.

        dotnet restore

3. Добавьте секрет с именем *Azure: SignalR: ConnectionString* в диспетчер секретов. Этот секрет будет содержать строку подключения для получения доступа к ресурсам службы SignalR. *Azure: SignalR:ConnectionString* — это ключ конфигурации по умолчанию, который SignalR ищет для установления соединения. Замените значение в приведенной ниже команде на строку подключения для вашего ресурса службы SignalR.

    Эта команда должна выполняться в том же каталоге, что и файл *CSPROJ*.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString Endpoint=<Your endpoint>;AccessKey=<Your access key>;    
    ```

    Диспетчер секретов будет использоваться только для тестирования веб-приложения во время его локального размещения. В следующем руководстве вы развернете веб-приложение чата в Azure. После развертывания веб-приложения в Azure вы используете параметр приложения вместо сохранения строки подключения с помощью диспетчера секретов.

4. Откройте файл *Startup.cs* и обновите метод `ConfigureServices` для использования службы Azure SignalR, вызвав метод `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Без передачи параметра `AddAzureSignalR()` этот код использует ключ конфигурации по умолчанию, *Azure: SignalR: ConnectionString* для строки подключения ресурса SignalR.

5. Кроме того, в файле *Startup.cs* обновите метод `Configure`, заменив вызов к `app.UseStaticFiles()` следующим кодом, и сохраните файл.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Добавление класса концентратора

В SignalR концентратор является основным компонентом, предоставляющим набор методов, которые можно вызывать из клиента. В этом разделе необходимо определить класс концентратора с двумя методами: 

* `Broadcast`: этот метод осуществляет широковещательную рассылку сообщений всем клиентам.
* `Echo`: этот метод отправляет сообщение обратно в вызывающий объект.

Оба метода используют интерфейс `Clients`, предоставляемый пакетом SDK для ASP.NET SignalR Core. Этот интерфейс предоставляет доступ ко всем подключенным клиентам, позволяя принудительно отправлять в них содержимое.

1. В каталоге проекта добавьте новую папку с именем *Hub*. Добавьте новый файл с кодом концентратора с именем *Chat.cs* в новую папку.

2. Добавьте следующий код в файл *Chat.cs* для определения класса концентратора и сохраните файл. 

    Обновите пространство имен для этого класса, если используется имя проекта, отличное от *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-web-app-client-interface"></a>Добавление веб-интерфейса клиентского приложения

Пользовательский интерфейс клиента для этого приложения чата будет состоять из HTML и JavaScript в файле с именем *index.html* в каталоге *wwwroot*.

Скопируйте файл *index.html*, папки *css* и *scripts* из папки *wwwroot* [хранилища примеров ](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) в папку *wwwroot* своего проекта.

Основной код файла *index.html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Код в файле *index.html* вызывает `HubConnectionBuilder.build()` для установки HTTP-подключения к ресурсам Azure SignalR.

Если подключение выполнено успешно, это соединение передается в `bindConnectionMessage`, при этом добавляются обработчики событий для входящей передачи содержимого в клиент. 

`HubConnection.start()` запускает обмен данными с концентратором. После запуска обмена данными `onConnected()` добавляет обработчики событий кнопки. Эти обработчики используют это соединение, чтобы позволить клиенту передавать обновления содержимого для всех подключенных клиентов.

## <a name="add-a-development-runtime-profile"></a>Добавление профиля среды выполнения разработки

В этом разделе вы добавите среду разработки для ASP.NET Core. Дополнительные сведения о среде выполнения ASP.NET Core см. в статье [Работа с несколькими средами](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Создайте папку в проекте с именем *Properties*.

2. Добавьте новый файл с именем *launchSettings.json* со следующим содержимым и сохраните файл в папку.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Локальное создание и запуск приложения

1. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

2. После того как создание завершится, чтобы запустить веб-приложение локально, выполните следующую команду:

        dotnet run

    Приложение будет размещаться локально на порту 5000, как указано в профиле среды выполнения разработки:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Запустите два окна браузера и перейдите в каждом браузере по адресу `http://localhost:5000`. Вам будет предложено ввести свое имя. Введите имя клиента для обоих клиентов и протестируйте передачу содержимого сообщений между обоими клиентами по нажатию кнопки **Отправить**.

    ![Полное руководство для локальной среды](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к следующему руководству можно использовать ресурсы, созданные в этом руководстве.

В противном случае, если вы закончите работу с примером приложения из краткого руководства, вы можете удалить ресурсы Azure, созданные в текущем руководстве, чтобы избежать ненужных расходов. 

> [!IMPORTANT]
> Обратите внимание, что удаление группы ресурсов — необратимая операция, и все соответствующие ресурсы удаляются окончательно. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в имеющейся группе ресурсов, содержащей ресурсы, которые следует сохранить, можно удалить каждый ресурс отдельно в соответствующих колонках вместо удаления группы ресурсов.
> 
> 

Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

Введите имя группы ресурсов в текстовое поле **Фильтровать по имени...**. В инструкциях к этому краткому руководству была использована группа ресурсов с именем *SignalRTestResources*. В своей группе ресурсов в списке результатов щелкните **...**, а затем **Удалить группу ресурсов**.

   
![Delete](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Подтвердите операцию удаления ресурсов. Введите имя группы ресурсов и нажмите кнопку **Удалить**.
   
Через некоторое время группа ресурсов и все ее ресурсы будут удалены.



## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратком руководством вы создали ресурс службы Azure SignalR и использовали его в веб-приложении ASP.NET Core, чтобы в реальном времени передавать обновления содержимого для нескольких подключенных клиентов. Чтобы получить дополнительные сведения об использовании службы Azure SignalR, перейдите к следующему руководству, которое демонстрирует выполнение аутентификации.

> [!div class="nextstepaction"]
> [Tutorial: Azure SignalR Service authentication](./signalr-authenticate-oauth.md) (Руководство по аутентификации службы Azure SignalR)


