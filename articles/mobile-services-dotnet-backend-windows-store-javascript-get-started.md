<properties linkid="develop-mobile-tutorials-get-started" urlDisplayName="Get Started" pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

В этом учебнике показано, как добавить облачную серверную службу в универсальное приложение Windows с помощью мобильных служб Azure. В этом учебнике будут созданы новая мобильная служба и простое приложение *To do list* на HTML и JavaScript, которое хранит данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Информацию о создании мобильной службы, которая позволяет создавать в JavaScript серверную бизнес-логику, см. в соответствующем подразделе этого раздела о JavaScript.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started][mobile-services-windows-universal-get-started]]

Для работы с этим учебником требуется:

-   Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Доступна бесплатная пробная версия.

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Создание нового универсального приложения для Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого начала работы щелкните **Windows** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения для магазина Windows**.

    ![][]

    Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

    ![][1]

3.  Если вы еще не сделали этого, скачайте и установите [Visual Studio Professional 2013][Visual Studio Professional 2013] на локальном компьютере или виртуальной машине.

4.  В разделе **Загрузите и локально запустите ваше приложение и службу** выберите язык приложения для Магазина Windows, затем щелкните **Загрузить**.

    При этом загружается решение, содержащее проекты для мобильной службы и примера приложения *To do list*, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Тестирование приложения с помощью локальной мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet][mobile-services-dotnet-backend-test-local-service-dotnet]]

>[WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле default.js.

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

1.  В проекте общего кода откройте файл default.js, найдите код, создающий экземпляр [WindowsAzure.MobileServiceClient][WindowsAzure.MobileServiceClient], закомментируйте код, который создает этот клиент с использованием *localhost*, и раскомментируйте код, который создает клиент с использованием URL-адреса удаленной мобильной службы. Он выглядит следующим образом:

        var client = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXX-APPLICATION-KEY-XXXXXX"
                );

    Теперь клиент будет обращаться к мобильной службе, опубликованной в Azure.

2.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

3.  В приложении введите содержательный текст, например *Работа с учебником*, в поле **Вставить в TodoItem**, затем щелкните **Сохранить**.

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure.

4.  (Необязательно) В универсальном решении для Windows замените загружаемый при запуске проект по умолчанию другим приложением и снова нажмите клавишу **F5**.

    Обратите внимание, что данные, сохраненные на предыдущем этапе, загружаются из мобильной службы после запуска приложения.

## Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]

    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

Дополнительные сведения об универсальных приложениях Windows см. в разделе [Поддержка нескольких клиентов из одной мобильной службы][Поддержка нескольких клиентов из одной мобильной службы].



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Бесплатная пробная версия Azure]: http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
  [mobile-services-dotnet-backend-test-local-service-dotnet]: ../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [WindowsAzure.MobileServiceClient]: http://msdn.microsoft.com/library/azure/jj554219.aspx
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Поддержка нескольких клиентов из одной мобильной службы]: /ru-ru/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
