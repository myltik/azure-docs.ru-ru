<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

В этом учебнике показано, как добавить облачную серверную службу в приложение Android с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][]

Чтобы изучить этот учебник, необходимы [средства разработчика Android][средства разработчика Android], что включает интегрированную среду разработки Eclipse (IDE), подключаемый модуль средств разработки Android (ADT) и последнюю версию платформы Android. Требуется Android 4.2 или более поздняя версия.

Загруженный проект быстрого запуска содержит пакет SDK мобильных служб для платформы Android. Хотя для этого проекта требуется Android 4.2 или более поздней версии, для пакета SDK мобильных служб требуется только Android 2.2 или более поздней версии.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Скачивание мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1.  Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска в разделе **Выберите платформу** выберите **Android** и разверните элемент **Создание нового приложения Android**.

    ![][1]

2.  Если вы еще не сделали этого, скачайте и установите [Visual Studio Professional 2013][Visual Studio Professional 2013] или более поздней версии.

3.  Щелкните кнопку **Скачать** в разделе **Скачивание и публикация службы в облаке**.

    При этом загружается проект Visual Studio, реализующий мобильную службу. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

4.  Кроме того, скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запишите путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Создание нового приложения для Android

В этом разделе будет создано новое приложение на платформе Android, которое будет подключено к вашей мобильной службе.

1.  На [портале управления][портале управления] щелкните **Мобильные службы**, затем щелкните только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **Android** в разделе **Выбор платформы**, а затем разверните раздел **Создать новое приложение на Android**.

    ![][2]

3.  Если вы еще не сделали этого, загрузите и установите [Средств разработчика Android][средства разработчика Android] на ваш локальный компьютер или виртуальную машину.

4.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запуск приложения для Android

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2.  В Eclipse нажмите кнопку **Файл**, затем щелкните **Импорт**, разверните **Android**, щелкните пункт **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее.**

    ![][3]

3.  Нажмите кнопку **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК**, убедитесь, что отмечен проект TodoActivity, а затем нажмите кнопку **Готово**.

    ![][4]

    При этом файлы проекта импортируются в текущую рабочую область.

    ![][5]

4.  Для запуска проекта в эмуляторе Android в меню **Запуск** щелкните **Выполнить**.

    <div class="dev-callout"><strong>Примечание.</strong> <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.</p></div>

5.  В приложении введите содержательный текст, например *Завершение работы с учебником*, и нажмите кнопку **Добавить**.

    ![][6]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    <div class="dev-callout"><strong>Примечание.</strong> 
<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.</p>
</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]

    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Устранение неполадок серверной части в мобильных службах .NET][Устранение неполадок серверной части в мобильных службах .NET]

     Узнайте, как определять и исправлять неполадки, которые могут возникнуть в серверной части мобильных служб для .NET.



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-android-get-started/
  []: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
  [средства разработчика Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [портале управления]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Устранение неполадок серверной части в мобильных службах .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
