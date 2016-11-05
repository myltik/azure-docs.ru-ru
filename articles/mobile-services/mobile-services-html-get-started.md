---
title: Приступая к работе с мобильными службами Azure для приложений HTML/JavaScript | Microsoft Docs
description: Этот учебник поможет приступить к использованию мобильных служб Azure для разработки приложений HTML.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-html5
ms.devlang: javascript
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: glenga

---
# <a name="getting-started"> </a>Приступая к работе с мобильными службами
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

## Обзор
В этом учебнике показано, как добавить облачную серверную службу в приложение на HTML с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. Вы можете просмотреть видеоверсию данного учебника.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-the-Mobile-Services-HTML-Client/player]
> 
> 

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям HTML. Для приложения PhoneGap/Cordova см. [версию PhoneGap или Cordova](mobile-services-javascript-backend-phonegap-get-started.md) этого учебника.

## Предварительные требования
Ниже перечислены необходимые условия для выполнения действий, описанных в этом учебнике.

* Необходимо иметь один из следующих веб-серверов, работающих на локальном компьютере.
  
  * **В Windows**: IIS Express. IIS Express устанавливается [установщиком веб-платформы Майкрософт].
  * **В MacOS X**: Python, который уже должен быть установлен.
  * **В Linux**: Python. Необходимо установить [последнюю версию Python].
    
    Можно использовать любой веб-сервер для размещения приложения, однако ниже приведены веб-серверы, которые поддерживаются доступными для загрузки сценариями.
* Браузер, который поддерживает HTML5.
* Учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank).

## <a name="create-new-service"> </a>Создание мобильной службы
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения HTML
После создания мобильной службы вы можете следовать простым шагам быстрого запуска на классическом портале Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к своей мобильной службе.

В этом разделе будет создано новое приложение HTML, которое будет подключено к вашей мобильной службе.

1. На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.
2. На вкладке быстрого запуска нажмите кнопку **Windows** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение HTML**.
   
       ![][6]
   
       Здесь показаны три простых шага для создания и размещения приложения HTML, подключенного к вашей мобильной службе.
   
      ![][7]
3. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItems**.
4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.
   
      При этом загружаются файлы веб-сайта для примера приложения *To do list*, которое подключается к вашей мобильной службе. Сохраните сжатый файл на локальном компьютере и запомните путь к нему.
5. На вкладке **Настройка** убедитесь, что элемент `localhost` уже указан в списке **Разрешить запросы от имен узлов** в разделе **Общий доступ к ресурсам независимо от источника (CORS)**. Если этот элемент не указан, введите `localhost` в поле **Имя узла** и нажмите кнопку **Сохранить**.
   
      ![][9]
   
   > [!IMPORTANT]
   > Если развернуть приложение быстрого запуска на веб-сервере, отличном от localhost, необходимо добавить имя узла веб-сервера в список **Разрешить запросы имен узлов**. Дополнительную информацию см. в разделе [Общий доступ к ресурсам независимо от источника](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx).
   > 
   > 

## Размещение и запуск приложения на HTML
Последний раздел учебника посвящен размещению и запуску нового приложения на локальном компьютере.

1. Перейдите в место, где сохранены сжатые файлы проекта, разверните файлы на своем компьютере и запустите один из следующих файлов команд в подпапке **server**.
   
   * **launch-windows** (компьютеры с ОС Windows)
   * **launch-mac.command** (компьютеры с ОС Mac OS X)
   * **launch-linux.sh** (компьютеры с ОС Linux)
     
     > [!NOTE]
     > На компьютере под управлением Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск сценария. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.
     > 
     > 
     
     Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.
2. Откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> в веб-браузере, чтобы запустить приложение.
3. В приложении в поле *Введите новую задачу* введите осмысленный текст, например **Завершение работы с учебником**, и нажмите кнопку **Добавить**.
   
       ![][10]
   
       Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются во втором столбце в приложении.
   
   > [!NOTE]
   > Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле page.js.
   > 
   > 
4. На [классическом портале Azure] щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItems**.
   
       ![][11]
   
       Это позволяет просматривать данные, добавленные в таблицу приложением.
   
       ![][12]

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* **[Добавление проверки подлинности к приложению мобильных служб]** Узнайте, как проверять подлинность пользователей приложения с помощью поставщика удостоверений.
* **[Использование клиента HTML/JavaScript для мобильных служб Azure]** Узнайте больше о том, как использовать мобильные службы с HTML и JavaScript.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Добавление проверки подлинности к приложению мобильных служб]: mobile-services-html-get-started-users.md

[классическом портале Azure]: https://manage.windowsazure.com/
[установщиком веб-платформы Майкрософт]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[последнюю версию Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Использование клиента HTML/JavaScript для мобильных служб Azure]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx


<!---HONumber=AcomDC_0727_2016-->