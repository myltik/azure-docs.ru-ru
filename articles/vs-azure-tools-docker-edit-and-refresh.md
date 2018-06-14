---
title: Отладка приложений в локальном контейнере Docker | Документация Майкрософт
description: Узнайте, как вносить изменения в приложение, выполняемое в локальном контейнере Docker, обновлять контейнер с помощью функций правки и обновления, а также устанавливать точки останова.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: 7df8bb2dd61466fd9af6492277d54cf0ffdfeb0b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781400"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Отладка приложений в локальном контейнере Docker
## <a name="overview"></a>Обзор
Инструменты Visual Studio для Docker обеспечивают согласованное выполнение разработки и проверки вашего приложения в локальном контейнере Docker в Linux.
Не нужно перезапустить контейнер каждый раз при внесении изменений в код.
В этой статье рассказывается, как запускать веб-приложение ASP.NET Core в локальном контейнере Docker, вносить необходимые изменения и обновлять браузер для отображения изменений с помощью функций правки и обновления.
В ней также показано, как устанавливать точки останова для отладки.

> [!NOTE]
> Поддержка контейнера Windows будет реализована в следующем выпуске.
>
>

## <a name="prerequisites"></a>предварительным требованиям
Должны быть установлены следующие средства.

* [Последняя версия Visual Studio](https://www.visualstudio.com/downloads/)
* Установить [пакет SDK для Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122).

Для запуска контейнеров Docker локально потребуется локальный клиент Docker.
Можно воспользоваться версией [Docker Toolbox](https://www.docker.com/products/docker-toolbox), для которой требуется отключить Hyper-V. Также можно воспользоваться [бета-версией Docker для Windows](https://www.docker.com/get-docker), для которой требуются Hyper-V и Windows 10.

При использовании Docker Toolbox необходимо [настроить клиент Docker](vs-azure-tools-docker-setup.md).

## <a name="1-create-a-web-app"></a>1. Создание веб-приложения
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Добавление поддержки Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Изменение и обновление кода
Для быстрой итерации изменений можно запустить приложение в контейнере и продолжить внесение изменений, просматривая их так же, как в IIS Express.

1. Укажите для конфигурации решения значение `Debug` и нажмите клавиши **&lt;CTRL+F5>**, чтобы создать образ Docker и запустить его локально.

    После создания и запуска образа контейнера в контейнере Docker служба Visual Studio запустит веб-приложение в вашем браузере по умолчанию.
    Если вы пользуетесь браузером Microsoft Edge или по каким-либо причинам в работе возникают проблемы, ознакомьтесь с разделом [Устранение неполадок](vs-azure-tools-docker-troubleshooting-docker-errors.md).
2. Перейдите на страницу "О программе", где мы сможем внести наши изменения.
3. Вернитесь в Visual Studio и откройте `Views\Home\About.cshtml`.
4. Добавьте следующее HTML-содержимое в конец файла и сохраните изменения.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. При просмотре окна вывода, когда сборка .NET завершена и отображаются следующие строки, вернитесь в браузер и обновите страницу "О программе".

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Изменения применены!

## <a name="4-debug-with-breakpoints"></a>4. Отладка с использованием точек останова
Часто после внесения изменений требуется выполнить дополнительные проверки, используя средства отладки Visual Studio.

1. Вернитесь в Visual Studio и откройте `Controllers\HomeController.cs`
2. Замените содержимое метода About() следующим кодом:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Установите точку останова слева от строки `string message`…
4. Чтобы начать отладку, нажмите клавишу **&lt;F5>**.
5. Перейдите на страницу "О программе", чтобы достигнуть точки останова.
6. Переключитесь в Visual Studio, чтобы просмотреть точку останова и проверить значение сообщения.

   ![][2]

## <a name="summary"></a>Сводка
Используя [инструменты Visual Studio 2015 для Docker](https://aka.ms/DockerToolsForVS), вы повышаете производительность за счет работы локально, а также получаете реалистичную рабочую среду для разработки в контейнере Docker.

## <a name="troubleshooting"></a>Устранение неполадок
[Устранение неполадок при разработке в Visual Studio Docker](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Дополнительные сведения об использовании Docker с Visual Studio, Windows и Azure
* [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) (Инструменты Docker для Visual Studio) — разработка кода .NET Core в контейнере.
* [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) (Инструменты Docker для Visual Studio Team Services) — сборка и развертывание контейнеров Docker.
* [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) (Инструменты Docker для Visual Studio Code) — языковые службы для редактирования файлов Docker с дополнительными сценариями E2E (ожидаются в ближайшее время).
* [Windows Container Information](http://aka.ms/containers)(Сведения о контейнерах Windows) — сведения о Windows Server и Nano Server.
* [Служба контейнеров Azure](https://azure.microsoft.com/services/container-service/)  -  [общие сведения о службе контейнеров Azure](http://aka.ms/AzureContainerService).
* Дополнительные примеры работы с Docker см. в статье [Working with Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) (Работа с Docker), описывающей [демонстрационный проект](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Дополнительные инструкции по быстрому началу работы с помощью средств разработчика Azure из демонстрационного проекта HealthClinic.biz см. [здесь](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Различные инструменты Docker
[Some great docker tools (Steve Lasker's blog) (Несколько отличных инструментов Docker — блог Стива Ласкера (Steve Lasker))](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Рекомендуемые статьи
[Introduction to Microservices from NGINX (Введение в микрослужбы от NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Презентации
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Стив Ласкер: презентация по Visual Studio и Docker E2E — Лас-Вегас, 2016 г.)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Введение в ASP.NET Core (сборка 2016))
* [Developing .NET apps in containers, Channel 9 (Разработка приложений .NET в контейнерах, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
