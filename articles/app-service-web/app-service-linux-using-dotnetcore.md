---
title: "Использование .NET Core в веб-приложении на платформе Linux | Документация Майкрософт"
description: "Использование .NET Core в веб-приложении на платформе Linux."
keywords: "служба приложений azure, веб-приложение, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Использование .NET Core в веб-приложении Azure на платформе Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Веб-приложение](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro) в Linux предоставляет масштабируемую службу размещения с самостоятельной установкой исправлений на основе операционной системы Linux. Это руководство содержит пошаговые инструкции по созданию приложения [.NET Core](https://docs.microsoft.com/aspnet/core/) в виде веб-приложении Azure на платформе Linux. 

![Веб-приложения на платформе Linux][10]

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux.

## <a name="prerequisites"></a>Предварительные требования ##

Для работы с этим руководством: 

* Установите [пакет SDK для .NET Core](https://www.microsoft.com/net/download/core).
* Установите [Git](https://git-scm.com/downloads).

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>Создание локального приложения .NET Core ##

Запустите новый сеанс терминала. Создайте каталог `hellodotnetcore` и перейдите в него. Затем введите следующую команду. 

```
dotnet new web
``` 

  Она создает в текущем каталоге три файла (*hellodotnetcore.csproj*, *Program.cs* и *Startup.cs*) и одну пустую папку (*wwwroot*). Содержимое файла с расширением `.csproj` должно выглядеть следующим образом. 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

Так как это приложение является веб-приложением, ссылка на пакет ASP.NET Core была автоматически добавлена в файл *hellodotnetcore.csproj*. Номер версии пакета настроен в соответствии с выбранной платформой. В этом примере используется ссылка на ASP.NET Core версии 1.1.2, так как используется .NET Core 1.1.

## <a name="build-and-test-the-application-locally"></a>Сборка и тестирование приложения в локальной среде ##

Можно выполнить сборку приложения .NET Core, выполнив команду `dotnet restore`, а затем запустить его командой `dotnet run`, как показано ниже.

```
dotnet restore
dotnet run
```


При запуске приложения будет отображено сообщение о том, что приложение ожидает передачи входящих запросов через порт. 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Протестируйте его, перейдя по адресу `http://localhost:5000/` в браузере. Если все работает правильно, вы увидите сообщение "Hello World!" после перехода.

![Тестирование с помощью браузера][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Создание приложения .NET Core на портале Azure ##

Сначала необходимо создать пустое веб-приложение. Войдите на [портал Azure](https://portal.azure.com/) и создайте [веб-приложение на платформе Linux](https://portal.azure.com/#create/Microsoft.AppSvcLinux).

![Создание веб-приложения][1]

Когда откроется страница **Создание**, укажите данные веб-приложения.

![Выбор стека времени выполнения .NET Core][2]

Используйте следующую таблицу в качестве руководства по заполнению страницы **Создание**, а затем нажмите кнопки **ОК** и **Создать**, чтобы создать приложение.

| Настройка      | Рекомендуемое значение  | Описание                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| Имя приложения. | hellodotnetcore  | Имя приложения. Это имя должно быть уникальным. |
| Подписки | Выберите существующую подписку. | Подписка Azure. |
| Группа ресурсов | myResourceGroup |  Группа ресурсов Azure, в которую будет помещено веб-приложение. |
| План обслуживания приложения | Имя существующего плана службы приложений. |  План службы приложений.  |
| Настроить контейнер | .NET Core 1.1 | Тип контейнера для этого веб-приложения: "Встроенный", "Docker" или "Частный реестр". |
| Источник образа  | Встроены  |  Источник образа. |
| Стек времени выполнения  | .NET Core 1.1  | Стек времени выполнения и версия.  |

## <a name="deploy-your-application-via-git"></a>Развертывание приложения с помощью Git ##

Используете Git для развертывания приложения .NET Core в веб-приложение службы приложений Azure на платформе Linux.

Для нового веб-приложения Azure уже настроено развертывание Git. URL-адрес развертывания Git можно найти, перейдя по приведенному ниже URL-адресу после вставки имение своего веб-приложения.

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

URL-адрес Git имеет следующий вид в соответствии с именем вашего веб-приложения.

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

Выполните следующие команды, чтобы развернуть локальное приложение в веб-приложение Azure. 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

Не нужно отправлять какие-либо файлы в каталогах *bin /* или *obj/*, так как при размещении исходных файлов приложения в Azure его сборка была выполнена в облаке. После завершения сборки двоичные файлы копируются в каталог приложения в */home/сайт/wwwroot/*.

Убедитесь, что операции удаленного развертывания успешно выполнены. Операции отправки могут занимать некоторое, так как разрешение пакетов и процесс сборки выполняются в облаке. Вы увидите несколько сообщений о состоянии, включая сообщения о том, что файлы были скопированы. Результат должен выглядеть следующим образом.

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

После завершения развертывания перезапустите веб-приложение, чтобы задействовать развернутую службу. Для этого войдите на портал Azure перейдите к колонке **Обзор** веб-приложения. Нажмите кнопку **Перезапустить** на странице. Когда появится всплывающее окно, щелкните **Да** для подтверждения. Затем можно будет перейти к своему веб-приложению, как показано ниже.

![Обзор приложения .NET Core, развернутого в службе приложений Azure на платформе Linux][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

