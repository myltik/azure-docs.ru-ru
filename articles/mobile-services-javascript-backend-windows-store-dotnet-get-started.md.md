<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

В этом учебнике показано, как добавить облачную серверную службу в универсальное приложение Windows с помощью мобильных служб Azure. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений Windows для Windows и Windows Phone][].

В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых на платформе .NET, см. в разделе о серверной версии .NET.

> В этом разделе показано, как создать новый проект мобильной службы и приложение Магазина Windows с помощью портала управления Azure. Используя Visual Studio 2013, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. на странице [Краткое руководство: Добавление мобильной службы (серверная часть JavaScript)][].

> Чтобы добавить мобильную службу в проект приложения Windows Phone 8.0 или Магазина Windows Phone 8.1, см. раздел [Приступая к работе с данными для Windows Phone][].

[WACOM.INCLUDE [mobile-services-windows-universal-get-started][]]

Для работы с этим учебником требуется:

-   Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][].
-   [Visual Studio 2013 Express для Windows][]

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## Создание нового универсального приложения для Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого начала работы щелкните **Windows** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения для магазина Windows**.

    ![][]

    Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

    ![][1]

3.  Если это еще не выполнено, загрузите и установите на локальном компьютере или виртуальной машине [Visual Studio 2013 Express для Windows][].

4.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

5.  В разделе **Загрузить и запустить приложение** выберите язык для своего приложения, а затем нажмите кнопку **Загрузить**.

    При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите приложение Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app][]]

> [WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.

## Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][]
    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][]
    Сведения об отправке в приложение простейших push-уведомлений.

Дополнительные сведения о б универсальных приложениях Windows см. в разделе [Поддержка нескольких клиентов из одной мобильной службы][].

<!-- Anchors. -->  
<!-- URLs. -->
<!-- Images. -->
  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Создание универсальных приложений Windows для Windows и Windows Phone]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx
  [Краткое руководство: Добавление мобильной службы (серверная часть JavaScript)]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn263180.aspx
  [Приступая к работе с данными для Windows Phone]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-data
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio 2013 Express для Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
  [mobile-services-javascript-backend-run-app]: ../includes/mobile-services-javascript-backend-run-app.md
  [Приступая к работе с данными]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Приступая к работе с аутентификацией]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Приступая к работе с push-уведомлениями]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [Поддержка нескольких клиентов из одной мобильной службы]: /en-us/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
