<properties
   pageTitle="Отладка приложений в локальном контейнере Docker | Microsoft Azure"
   description="Узнайте, как вносить изменения в приложение, выполняемое в локальном контейнере Docker, обновлять контейнер с помощью функций правки и обновления, а также устанавливать точки останова."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# Отладка приложений в локальном контейнере Docker

## Обзор
Инструменты Visual Studio для Docker обеспечивают согласованное выполнение разработки и проверки вашего приложения локально в контейнере Docker в Linux. Не нужно перезапустить контейнер каждый раз при внесении изменений в код. В этой статье рассказывается, как запускать веб-приложение ASP.NET Core в локальном контейнере Docker, вносить необходимые изменения и обновлять браузер для отображения сделанных изменений с помощью функций правки и обновления. В ней также будет показано, как устанавливать точки останова для отладки.

> [AZURE.NOTE] Поддержка контейнера Windows будет реализована в следующем выпуске.

## Предварительные требования
Должны быть установлены следующие средства.

- [Visual Studio 2015 с обновлением 2](https://go.microsoft.com/fwlink/?LinkId=691978).
- [Visual Studio 2015 с обновлением 3](https://go.microsoft.com/fwlink/?LinkId=691129).
- [Пакет SDK для Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122).

Для запуска контейнеров Docker локально потребуется локальный клиент Docker. Можно воспользоваться выпуском [панели элементов Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox), для которой требуется отключить Hyper-V, или можно воспользоваться [бета-версией Docker для Windows](https://beta.docker.com), для которой требуется Hyper-V и Windows 10.

При использовании панели элементов Docker Toolbox необходимо выполнить [настройку клиента Docker](./vs-azure-tools-docker-setup.md).

## 1\. Создание веб-приложения

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2) Добавление поддержки Docker

[AZURE.INCLUDE [Добавление поддержки Docker](../includes/vs-azure-tools-docker-add-docker-support.md)]


## 3) Изменение и обновление кода

Для быстрой итерации изменений можно запустить приложение в контейнере и продолжить внесение изменений, просматривая их так же, как в IIS Express.

1. Укажите для конфигурации решения значение `Debug` и нажмите клавиши **&lt;CTRL+F5>**, чтобы создать образ Docker и запустить его локально.

    Когда образ контейнера будет построен и запущен в контейнере Docker, Visual Studio запустит веб-приложение в вашем браузере по умолчанию. Если вы пользуетесь браузером Microsoft Edge или если у вас возникают проблемы, см. раздел [Устранение неполадок](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Перейдите на страницу "О программе", где мы сможем внести наши изменения.

1. Вернитесь в Visual Studio и откройте `Views\Home\About.cshtml`.

1. Добавьте следующее HTML-содержимое в конец файла и сохраните изменения.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	При просмотре окна вывода, когда сборка .NET завершена и отображаются следующие строки, вернитесь в браузер и обновите страницу "О программе".

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	Изменения применены!

## 4\. Отладка с использованием точек останова

Часто после внесения изменений требуется выполнить дополнительные проверки, используя средства отладки Visual Studio.

1.	Вернитесь в Visual Studio и откройте `Controllers\HomeController.cs`.

1.  Замените содержимое метода About() следующим кодом:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Установите точку останова слева от строки `string message`…

1.  Чтобы начать отладку, нажмите клавишу **&lt;F5>**.

1.  Перейдите на страницу "О программе", чтобы достигнуть точки останова.

1.  Переключитесь в Visual Studio, чтобы просмотреть точку останова и проверить значение сообщения.

	![][2]

##Сводка

Используя [инструменты Visual Studio 2015 для Docker](https://aka.ms/DockerToolsForVS), вы повышаете производительность за счет работы локально, а также получаете реалистичную рабочую среду для разработки в контейнере Docker.

## Устранение неполадок

[Устранение неполадок при разработке в Visual Studio Docker](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Дополнительные сведения об использовании Docker с Visual Studio, Windows и Azure

- [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) (Инструменты Docker для Visual Studio) — разработка кода .NET Core в контейнере.
- [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) (Инструменты Docker для Visual Studio Team Services) — сборка и развертывание контейнеров Docker.
- [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) (Инструменты Docker для Visual Studio Code) — языковые службы для редактирования файлов Docker с дополнительными сценариями E2E (ожидаются в ближайшее время).
- [Windows Container Information](http://aka.ms/containers) (Сведения о контейнерах Windows) — сведения о Windows Server и Nano Server.
- [Служба контейнеров Azure](https://azure.microsoft.com/services/container-service/) — [общие сведения о службе контейнеров Azure](http://aka.ms/AzureContainerService).
-    Дополнительные примеры работы с Docker см. в статье [Работа с Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker), описывающей [демонстрационный проект](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Дополнительные примеры из демонстрационного проекта HealthClinic.biz см. на странице [Примеры использования средств разработчика Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## Различные инструменты Docker

[Some great docker tools (Steve Lasker's blog) (Несколько отличных инструментов Docker — блог Стива Ласкера (Steve Lasker))](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Рекомендуемые статьи

[Introduction to Microservices from NGINX (Введение в микрослужбы от NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## Презентации

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Стив Ласкер: презентация по Visual Studio и Docker E2E — Лас-Вегас, 2016 г.)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (Введение в ASP.NET Core @ сборка 2016 г.)](https://channel9.msdn.com/Events/Build/2016/B810)
- [Developing .NET apps in containers, Channel 9 (Разработка приложений .NET в контейнерах, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0921_2016-->