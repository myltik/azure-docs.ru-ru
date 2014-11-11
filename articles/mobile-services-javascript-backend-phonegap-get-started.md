<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

В этом учебнике показано, как добавить облачную серверную службу в приложение с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе.

Снимок экрана завершенного приложения приведен ниже:

![][0]

### <a name="additional-requirements"></a>Дополнительные требования

Для работы с этим учебником необходимы средства PhoneGap (для проектов Windows Phone 8 требуется версия 3.2+).

PhoneGap поддерживает разработку для нескольких платформ. Помимо самих средств PhoneGap необходимо установить средства для каждой используемой платформы.

-   Windows Phone: установите [Visual Studio 2012 Express для Windows Phone][Visual Studio 2012 Express для Windows Phone]
-   iOS: установите [Xcode][Xcode] (требуется версия не ниже 4.4)
-   Android: установите [средства разработчика Android][средства разработчика Android]
    <br/>(Пакет SDK для мобильных служб для Android поддерживает приложения для Android 2.2 или более поздней версии. ОС Android 4.2 или более поздняя версия требуется для запуска приложения быстрого запуска.)

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Создание нового приложения</span>Создание нового приложения PhoneGap

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение PhoneGap, подключенное к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **PhoneGap** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение PhoneGap**.

    ![][1]

    Здесь показаны три простых шага для создания приложения PhoneGap, подключенного к вашей мобильной службе.

    ![][2]

3.  Если вы еще не сделали этого, скачайте и установите PhoneGap и средства разработки хотя бы для одной платформы (Windows Phone, iOS или Android).

4.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.

5.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом загружается проект для примера приложения *To do list*, подключенного к вашей мобильной службе, а также пакет Mobile Services SDK для JavaScript. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения PhoneGap

Последний раздел учебника — построение и выполнение нового приложения.

1.  Перейдите в расположение, где сохранены сжатые файлы проекта и извлеките файлы на компьютере.

2.  Откройте и запустите проект, следуя приведенным ниже инструкциям для каждой платформы.

    -   **Windows Phone 8**

    1.  Windows Phone 8: откройте SLN-файл в папке **platforms\\wp8** в Visual Studio 2012 Express для Windows Phone.

    2.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

        ![][3]

    -   **iOS**

    1.  Откройте проект в папке **platforms/ios** в Xcode.

    2.  Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

        ![][0]

    -   **Android**

        1.  В Eclipse нажмите кнопку **Файл**, затем щелкните **Импорт**, разверните **Android**, щелкните пункт **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее.**

        2.  Нажмите кнопку **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК**, убедитесь, что отмечен проект TodoActivity, а затем нажмите кнопку **Готово**.

            При этом файлы проекта импортируются в текущую рабочую область.

            </p>
        3.  Для запуска проекта в эмуляторе Android в меню **Запуск** щелкните **Выполнить**.

            ![][4]

        > [WACOM.NOTE]Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

3.  После запуска приложения в одном из указанных выше мобильных эмуляторов введите какой-либо текст в текстовое поле, а затем щелкните **Добавить**.

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу **TodoItem**. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

    <div class="dev-callout"><strong>Важно!</strong> <p>Изменения в этом проекте платформы будут перезаписаны при повторной сборке основного проекта с использованием инструментов PhoneGap. Вместо этого внесите изменения в корневом каталоге www проекта, как описано в приведенном ниже разделе.</p></div>

4.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][5]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][6]

## Обновление приложения и повторная сборка проектов для каждой платформы

1.  Внесите изменения в файлы кода в каталоге www, которым в данном случае является каталог todolist/www.

2.  Убедитесь, что все инструменты целевой платформы доступны по системному пути.

3.  Откройте окно командной строки в корневом каталоге проекта и выполните одну из следующих команд для конкретной платформы:

    -   **Windows Phone**

        Выполните следующую команду из командной строки для разработчика Visual Studio:

            phonegap local build wp8

    -   **iOS**

        Откройте терминал и выполните следующую команду:

            phonegap local build ios

    -   **Android**

        Откройте окно командной строки или терминала и выполните следующую команду:

            phonegap local build android

4.  Откройте каждый проект в соответствующей среде разработки, как описано в предыдущем разделе.

> [WACOM.NOTE]Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле js/index.js.

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

<!-- Images. URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
  [Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [средства разработчика Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
  [2]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
  [3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
  [4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png
  [5]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-html-get-started-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-html-get-started-users
