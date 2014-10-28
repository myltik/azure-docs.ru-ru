<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin iOS apps" pageTitle="Get Started with Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin iOS apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

В этом учебнике показано, как добавить облачную серверную службу в приложение Xamarin iOS с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

> [WACOM.NOTE]В этом разделе показано, как создать новый проект мобильной службы с помощью портала управления Azure. Используя Visual Studio 2013 с обновлением 2, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. на странице [Краткое руководство: Добавление мобильной службы (внутренний сервер .NET)][Краткое руководство: Добавление мобильной службы (внутренний сервер .NET)]

Снимок экрана завершенного приложения приведен ниже:

![][]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям Xamarin iOS.

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
> Для работы с этим учебником требуется [Visual Studio Professional 2013][Visual Studio Professional 2013]. Доступна бесплатная пробная версия.

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Создание нового приложения Xamarin iOS

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе предстоит скачать новое приложение Xamarin iOS и проект службы для мобильной службы.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **Xamarin** в разделе **Выбор платформы**, а затем разверните раздел **Создание нового приложения Xamarin**.

    ![][1]

    Здесь показаны три простых шага для создания приложения на Xamarin iOS, подключенного к вашей мобильной службе.

    ![][2]

3.  Если вы еще не сделали этого, скачайте и установите [Visual Studio Professional 2013][Visual Studio Professional 2013] на локальном компьютере или виртуальной машине.

4.  Скачайте и установите [Xcode][Xcode] v4.4 или более позднюю версию и [Xamarin Studio][Xamarin Studio]. Также можно использовать Xamarin для Visual Studio.

5.  В разделе **Скачивание и публикация службы в облаке** выберите **iOS** и щелкните кнопку **Скачать**.

    При этом загружается решение, содержащее проекты для мобильной службы и примера приложения *To do list*, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6.  Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Запуск приложения Xamarin iOS

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите к проекту клиента в решении мобильной службы в Visual Studio или Xamarin Studio.

    ![][3]

    ![][4]

2.  Нажмите кнопку **Запустить**, чтобы создать проект клиента и запустить приложение в эмуляторе iPhone.

3.  В приложении введите содержательный текст, например, *Работа с учебником* и щелкните значок "плюс" (**+**).

    ![][5]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

> [WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле C# QSTodoService.cs.

## Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Серверная версия JavaScript]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started
  [Краткое руководство: Добавление мобильной службы (внутренний сервер .NET)]: http://msdn.microsoft.com/ru-ru/library/windows/apps/dn629482.aspx
  []: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [3]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
  [4]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
  [5]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
