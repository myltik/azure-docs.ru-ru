<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение Windows Phone 8 с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение <em>To do list</em>, хранящее данные приложения в новой мобильной службе.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике. В этом видео Ника Харриса (Nick Harris) приведена базовая информация о мобильных службах, а также указания по созданию первой мобильной службы и подключению к ней приложения для Магазина Windows.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">13:18:00</span></div>

</div>

В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых на платформе .NET, см. в подразделе [Серверная версия .NET][Серверная версия .NET] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure]. Чтобы создать новое приложение для Windows Phone 8.1, нужна программа Visual Studio 2013 с обновлением 2 или более поздняя версия.

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">Создание нового приложения</span>Создание нового приложения для Windows Phone

</h2>
После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение Windows Phone 8, которое будет подключаться к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого начала работы щелкните **Windows Phone 8** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения Windows Phone 8**.

    ![][1]

    Здесь показаны три простых шага для создания приложения Windows Phone, подключаемого к вашей мобильной службе.

    ![][2]

3.  Если это еще не выполнено, загрузите и установите на локальном компьютере [Visual Studio 2012 Express для Windows Phone][Visual Studio 2012 Express для Windows Phone].

    > [WACOM.NOTE]Чтобы создать приложение для Windows Phone 8.1, требуется Visual Studio 2013 с обновлением 2.

4.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

5.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запуск приложения для Windows Phone

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите в расположение, где были сохранены сжатые файлы проекта, извлеките файлы на компьютере и откройте файл решения в Visual Studio.

2.  (Необязательно). Если вы создаете приложение для Windows Phone 8.1, щелкните правой кнопкой проект в обозревателе решений, выберите **Свойства**, задайте для элемента **Целевая версия ОС Windows Phone** значение **Windows Phone 8.1**, а затем щелкните **Да**, чтобы обновить проект.

3.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите какой-либо осмысленный текст, например *Завершение работы с учебником*, и щелкните **Сохранить**.

    ![][3]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    > [WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле MainPage.xaml.cs.

5.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][4]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][5]

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с синхронизацией автономных данных][Приступая к работе с синхронизацией автономных данных]
    <br/>Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    <br/>Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Серверная версия .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFru-ru%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Приступая к работе с синхронизацией автономных данных]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
