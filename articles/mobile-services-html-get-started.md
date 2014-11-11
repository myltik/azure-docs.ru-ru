<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение на HTML с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение <em>To do list</em>, хранящее данные приложения в новой мобильной службе. Чтобы просмотреть видеоверсию данного учебника, щелкните расположенный справа клип.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">3:51:00</span></div>

</div>

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям HTML.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

### Дополнительные требования

-   В этом учебнике требуется наличие одного из следующих веб-серверов, запущенных на локальном компьютере:

    -   **В Windows**: IIS Express. IIS Express устанавливается [установщиком веб-платформы Майкрософт][установщиком веб-платформы Майкрософт].
    -   **В MacOS X**: Python, который уже должен быть установлен.
    -   **В Linux**: Python. Необходимо установить [последнюю версию Python][последнюю версию Python].

    Можно использовать любой веб-сервер для размещения приложения, однако ниже приведены веб-серверы, которые поддерживаются доступными для загрузки сценариями.

-   Браузер, который поддерживает HTML5.

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">Создание приложения</span>Создание приложения на HTML

</h2>
После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение HTML, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **Windows** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение HTML**.

    ![][1]

    Здесь показаны три простых шага для создания и размещения приложения HTML, подключенного к вашей мобильной службе.

    ![][2]

3.  Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.

4.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом загружаются файлы веб-сайта для примера приложения *To do list*, которое подключается к вашей мобильной службе. Сохраните сжатый файл на локальном компьютере и запомните путь к нему.

5.  На вкладке **Настройка** убедитесь, что элемент `localhost` уже указан в списке **Разрешить запросы от имен узлов** в разделе **Общий доступ к ресурсам независимо от источника (CORS)**. Если он не указан, введите `localhost` в поле **Имя узла** и щелкните кнопку **Сохранить**.

    ![][3]

    <div class="dev-callout"><b>Примечание.</b>
    <p>Если развернуть приложение быстрого запуска на веб-сервере, отличном от localhost, необходимо добавить имя узла веб-сервера в список <strong>Разрешить запросы имен узлов</strong>. Дополнительную информацию см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn155871.aspx" target="_blank">Общий доступ к ресурсам независимо от источника</a>.</p>
</div>

## Размещение и запуск приложения на HTML

Последний раздел учебника посвящен размещению и запуску нового приложения на локальном компьютере.

1.  Перейдите в место, где сохранены сжатые файлы проекта, разверните файлы на своем компьютере и запустите один из следующих файлов команд в подпапке **server**.

    -   **launch-windows** (компьютеры с ОС Windows)
    -   **launch-mac.command** (компьютеры с ОС Mac OS X)
    -   **launch-linux.sh** (компьютеры с ОС Linux)

    <div class="dev-callout"><b>Примечание.</b>
    <p>На компьютере с операционной системой Windows в ответ на запрос PowerShell на запуск сценария нажмите клавишу R. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.</p>
</div>

    Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

2.  Откройте URL-адрес [http://localhost: 8000/][http://localhost: 8000/] в веб-браузере, чтобы запустить приложение.

3.  В приложении в поле **Введите новую задачу** введите осмысленный текст, например *Завершение работы с учебником*, и нажмите кнопку **Добавить**.

    ![][4]

    Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются во втором столбце в приложении.

    <div class="dev-callout"> 
<b>Примечание.</b> 
<p>Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле app.js.</p> 
</div>

4.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][5]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][6]

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   **[Приступая к работе с данными][Приступая к работе с данными]**

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   **[Вызов настраиваемого API из приложения на HTML][Вызов настраиваемого API из приложения на HTML]**

    Подключение приложения на HTML к настраиваемому API, размещенному в мобильных службах.

-   **[Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]**

    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   **[Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]**

    Узнайте больше о том, как использовать мобильные службы с HTML и JavaScript.



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [установщиком веб-платформы Майкрософт]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [последнюю версию Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [http://localhost: 8000/]: http://localhost:8000/
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
  [Вызов настраиваемого API из приложения на HTML]: /ru-ru/documentation/articles/mobile-services-html-call-custom-api
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client
