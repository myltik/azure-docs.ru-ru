<properties
   pageTitle="Отладка приложений в локальном контейнере Docker | Microsoft Azure"
   description="Узнайте, как вносить изменения в приложение, выполняемое в локальном контейнере Docker, обновлять контейнер с помощью функций правки и обновления, а также устанавливать точки останова."
   services="visual-studio-online"
   documentationCenter="na"
   authors="AllenClark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/13/2016"
   ms.author="allclark" />

# Отладка приложений в локальном контейнере Docker

## Обзор
Инструменты Visual Studio для Docker обеспечивают согласованное выполнение разработки и проверки вашего приложения локально в контейнере Docker в Linux. Не нужно перезапустить контейнер каждый раз при внесении изменений в код. В этой статье рассказывается, как запускать веб-приложение ASP.NET Core в локальном контейнере Docker, вносить необходимые изменения и обновлять браузер для отображения сделанных изменений с помощью функций правки и обновления. В ней также будет показано, как устанавливать точки останова для отладки.

> [AZURE.NOTE] Поддержка контейнера Windows будет реализована в следующем выпуске.

## Предварительные требования
Должны быть установлены следующие средства.

- [Visual Studio 2015 с обновлением 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Средства Visual Studio 2015 для Docker](https://aka.ms/DockerToolsForVS)

Для запуска контейнеров Docker локально потребуется локальный клиент Docker. Можно воспользоваться выпуском [панели элементов Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox), для которой требуется отключить Hyper-V, или можно воспользоваться [бета-версией Docker для Windows](https://beta.docker.com), для которой требуется Hyper-V и Windows 10.

При использовании панели элементов Docker Toolbox необходимо выполнить [настройку клиента Docker](./vs-azure-tools-docker-setup.md).

## Изменение приложения, запущенного в локальном контейнере Docker
Инструменты Visual Studio 2015 для Docker позволяют разработчикам веб-приложений ASP .NET Core RC2 тестировать и запускать приложения в контейнере Docker, вносить изменения в Visual Studio и обновлять браузер для просмотра изменений в приложении, запущенном в контейнере. С помощью .NET Core и инструментов Visual Studio для Docker версии 0.20 также можно установить точки останова для кода, выполняемого в контейнере Docker.

1. В меню Visual Studio выберите **Файл > Создать > Проект**.

1. В разделе **Шаблоны** диалогового окна **Новый проект** выберите **Visual C# > Веб-проект**.

1. Выберите **веб-приложение ASP.NET Core (.NET Core)**.

1. Присвойте новому приложению имя (или оставьте имя по умолчанию) и нажмите кнопку **ОК**.

1. В области **Шаблоны ASP.NET Core** выберите **Веб-приложение** и нажмите кнопку **ОК**.

1. Снимите флажок **Разместить в облаке**, так как Docker будет использоваться в качестве решения для развертывания.

1. В обозревателе решений Visual Studio щелкните проект правой кнопкой мыши и выберите пункты **Добавить > Поддержка Docker**.

	![][0]

1. В узле проекта будут созданы следующие файлы:

	![][1]

> [AZURE.NOTE] При использовании [бета-версии Docker для Windows](https://beta.docker.com) откройте файл Properties\\Docker.props и удалите значение по умолчанию, а затем перезапустите Visaul Studio, чтобы значение вступило в силу. ![][2]

##Редактирование и обновление
Для быстрой итерации изменений можно запустить приложение в контейнере и продолжить внесение изменений, просматривая их так же, как в IIS Express.

1. Укажите для конфигурации решения значение `Debug` и нажмите сочетание клавиш **&lt;CTRL + F5>**, чтобы создать образ Docker и запустить его локально. См. окно вывода, используя сборку.

1. Когда образ контейнера будет построен и запущен в контейнере Docker, Visual Studio попытается запустить веб-приложение в вашем браузере по умолчанию. Если вы пользуетесь браузером Microsoft Edge, или если возникают проблемы, см. раздел [Устранение неполадок](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Вернитесь в Visual Studio и откройте `Views\Home\About.cshtml`.

1. Добавьте следующее HTML-содержимое в конец файла и сохраните изменения:

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	При просмотре окна вывода, когда сборка .NET завершена и отображается `Application started. Press Ctrl+C to shut down`, вернитесь в браузер и обновите страницу.

1.	Вы должны увидеть, что изменения применены.

##Отладка точки останова
Часто после внесения изменений требуется выполнить дополнительные проверки, используя средства отладки Visual Studio.

1.	Вернитесь в Visual Studio и откройте `Controllers\HomeController.cs`.

1.  Замените содержимое метода About() следующим кодом:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Установите точку останова слева от строки `string message`…

1.  Чтобы начать отладку, нажмите клавишу **&lt; F5 >**.

1.  Перейдите на страницу "О программе", чтобы достигнуть точки останова.

1.  Переключитесь в Visual Studio, чтобы просмотреть точку останова и проверить значение сообщения.

	![][3]

##Сводка
Используя [инструменты Visual Studio 2015 для Docker](https://aka.ms/DockerToolsForVS), вы повышаете производительность за счет работы локально, а также получаете реалистичную рабочую среду для разработки в контейнере Docker.

## Устранение неполадок
[Устранение неполадок при разработке в Visual Studio Docker](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Дополнительные сведения об использовании Docker с Visual Studio, Windows и Azure

- [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) (Инструменты Docker для Visual Studio) — разработка кода .NET Core в контейнере.
- [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) (Инструменты Docker для Visual Studio Team Services) — сборка и развертывание контейнеров Docker.
- [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) (Инструменты Docker для Visual Studio Code) — языковые службы для редактирования файлов Docker с дополнительными сценариями E2E (ожидаются в ближайшее время).
- [Windows Container Information](http://aka.ms/containers) (Сведения о контейнерах Windows) — сведения о Windows Server и Nano Server.
- [Служба контейнеров Azure](https://azure.microsoft.com/services/container-service/) — [Общие сведения о службе контейнеров Azure](http://aka.ms/AzureContainerService).

## Различные инструменты Docker

[Some great docker tools (Steve Lasker's blog)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/) (Несколько отличных инструментов Docker — блог Стива Ласкера (Steve Lasker))

## Рекомендуемые статьи

[Introduction to Microservices from NGINX](https://www.nginx.com/blog/introduction-to-microservices/) (Введение в микрослужбы от NGINX)

## Презентации

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/) (Стив Ласкер: презентация по Visual Studio и Docker E2E — Лас-Вегас, 2016 г.)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Введение в ASP.NET Core @ сборка 2016 г.)
- [Developing .NET apps in containers, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/) (Разработка приложений .NET в контейнерах, Channel 9)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0518_2016-->