---
title: включение файла
description: включение файла
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 4a3f38e1423db0755d8c76f8850e41173d250f43
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33904992"
---
### <a name="create-a-console-application"></a>Создание консольного приложение

Если при создании ретранслятора вы отключили параметр"Требует авторизации клиента", запросы на URL-адрес гибридных подключений можно отправлять с помощью любого браузера. Для доступа к защищенным конечным точкам необходимо создать и передать маркер в заголовок `ServiceBusAuthorization`, который приведен ниже.

В Visual Studio создайте проект **Консольное приложение (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Добавление пакета ретранслятора NuGet

1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Выберите **Обзор** и выполните поиск по ключевой фразе **Microsoft.Azure.Relay**. В результатах поиска выберите **Ретранслятор Microsoft Azure**. 
3. Выберите **Установить** для завершения установки. Закройте диалоговое окно.

### <a name="write-code-to-send-requests"></a>Написание кода для отправки запросов

1. В начале файла Program.cs замените существующие операторы `using` следующими операторами `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Добавьте константы в класс `Program`, чтобы получить сведения о гибридном подключении. Замените заполнители в скобках значениями, которые получены при создании гибридного подключения. Необходимо использовать полное имя пространства имен.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Добавьте в класс `Program` следующий метод:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
    ```
4. В метод `Main` в классе `Program` добавьте следующую строку кода.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Файл Program.cs должен выглядеть следующим образом:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

