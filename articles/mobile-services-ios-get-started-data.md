<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении iOS. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения для iOS. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-ios">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения iOS][Загрузка проекта приложения iOS]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Добавление таблицы для хранения данных][Добавление таблицы для хранения данных]
4.  [Обновление приложения для использования мобильных служб][Обновление приложения для использования мобильных служб]
5.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

Для работы с данным учебником требуется следующее:

-   [Пакет SDK для мобильных служб для iOS][Пакет SDK для мобильных служб для iOS], [XCode 4.5][XCode 4.5] и iOS 5.0 или более поздних версий.
-   Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
    </p>
    </div>

## <a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на приложении [GetStartedWithData][GetStartedWithData], которое является приложением iOS. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам iOS. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1.  Скачайте [пример приложения][GetStartedWithData] GetStartedWithData.

2.  В Xcode откройте скачанный проект и просмотрите файл TodoService.m.

    Обратите внимание на восемь комментариев **/ / TODO::**, указывающих на действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

3.  Нажмите кнопку **Выполнить** (или сочетание клавиш Command + R) для повторного построения проекта и запуска приложения.

4.  В приложении введите какой-либо текст в текстовом поле, а затем нажмите кнопку **+**.

    ![][0]

    Обратите внимание, что сохраненный текст отображается в списке ниже.

## <a name="create-service"></a>Создание мобильной службы на портале управления

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Добавление новой таблицы в мобильную службу

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильной службы

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Тестирование работы приложения с новой мобильной службой

1.  В Xcode выберите эмулятор для развертывания (iPhone или iPad) и нажмите кнопку **Запуск** (или клавиши Command+R), чтобы повторить сборку проекта и запустить приложение.

    В результате начнет выполняться клиент мобильных служб Azure, созданный с использованием пакета SDK для iOS, который запрашивает элементы из мобильной службы.

2.  Так же как и ранее, введите текст в текстовое поле и нажмите кнопку **+**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3.  На [Портале управления][Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4.  Откройте вкладку **Данные**, а затем щелкните **Обзор**.

    ![][1]

    Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для iOS.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике показаны основы включения в iOS-приложении возможностей работы с данными в мобильных службах.

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
    <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
    <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников iOS:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    <br/>Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    <br/>Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.

<!-- Anchors. Images.  URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Загрузка проекта приложения iOS]: #download-app
  [Создание мобильной службы]: #create-service
  [Добавление таблицы для хранения данных]: #add-table
  [Обновление приложения для использования мобильных служб]: #update-app
  [Тестирование работы приложения с мобильными службами]: #test-app
  [Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [Портале управления]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
