<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение Windows Phone 8 с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
> Для работы с этим учебником требуется [Visual Studio Professional 2013][Visual Studio Professional 2013]. Доступна бесплатная пробная версия. Чтобы создать новое приложение для Windows Phone 8.1, нужна программа Visual Studio 2013 с обновлением 2 или более поздняя версия.

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения для Windows Phone

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение Windows Phone 8, которое будет подключаться к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого начала работы щелкните **Windows Phone 8** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения Windows Phone 8**.

    ![][1]

    Здесь показаны три простых шага для создания приложения Windows Phone, подключаемого к вашей мобильной службе.

    ![][2]

3.  Если вы еще не сделали этого, скачайте и установите [Visual Studio Professional 2013][Visual Studio Professional 2013] на локальном компьютере или виртуальной машине.

4.  В разделе **Загрузка и публикация службы в облаке** щелкните **Загрузить**.

    При этом загружается решение, содержащее проекты для мобильной службы и примера приложения *To do list*, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

5.  В разделе **Публикация службы в облаке** скачайте профиль публикации, сохраните скачанный файл на локальном компьютере и запомните путь к нему.

## Тестирование мобильной службы на локальном компьютере

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]Для запуска приложения для Windows Phone, подключающегося к локальной службе, необходимо выполнить дополнительную настройку. В этом разделе мы не показываем, как это сделать, но вы можете найти дополнительные сведения в разделе [Подключение к локальной веб-службе из эмулятора Windows Phone 8][Подключение к локальной веб-службе из эмулятора Windows Phone 8].

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  В проекте приложения для Windows Phone откройте файл App.xaml.cs, найдите код, создающий экземпляр [MobileServiceClient][MobileServiceClient], закомментируйте код, который создает этот клиент с использованием *localhost*, и раскомментируйте код, который создает клиент с использованием URL-адреса удаленной мобильной службы. Он выглядит следующим образом:

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    Теперь клиент будет обращаться к мобильной службе, опубликованной в Azure.

2.  (Необязательно) Если вы создаете приложение для Windows Phone 8.1, щелкните правой кнопкой проект в обозревателе решений, выберите **Свойства**, задайте для элемента **Целевая версия ОС Windows Phone** значение **Windows Phone 8.1**, а затем щелкните **Да**, чтобы обновить проект.

3.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите осмысленный текст, например *Завершение работы с учебником*, и нажмите кнопку **Сохранить**.

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    <div class="dev-callout"> 
    <b>Примечание</b> 
    <p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.</p> 
    </div>

![][3]

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения Windows Phone с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с устройства или эмулятора Windows Phone. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе][Настройка локального веб-сервера для подключения к локальной мобильной службе].

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с синхронизацией автономных данных][Приступая к работе с синхронизацией автономных данных]

    Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]

    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Устранение неполадок серверной части в мобильных службах .NET][Устранение неполадок серверной части в мобильных службах .NET]

     Узнайте, как определять и исправлять неполадки, которые могут возникнуть в серверной части мобильных служб для .NET.



  [Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Подключение к локальной веб-службе из эмулятора Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [MobileServiceClient]: http://msdn.microsoft.com/ru-ru/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Настройка локального веб-сервера для подключения к локальной мобильной службе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Приступая к работе с синхронизацией автономных данных]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Устранение неполадок серверной части в мобильных службах .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
