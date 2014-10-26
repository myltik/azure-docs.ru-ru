<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Уточнение запросов мобильных служб c разбиением по страницам

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению HTML. В этом учебнике методы запроса **Take** и **Skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию &mdash; 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными][Приступая к работе с данными].

1.  Запустите один из следующих командных файлов во вложенной папке **server** проекта, измененного в ходе изучения материала учебника [Приступая к работе с данными][Приступая к работе с данными].

    -   **launch-windows** (компьютеры с ОС Windows)
    -   **launch-mac.command** (компьютеры с ОС Mac OS X)
    -   **launch-linux.sh** (компьютеры с ОС Linux)

    <div class="dev-callout"><b>Примечание.</b>
    <p>На компьютере с операционной системой Windows в ответ на запрос PowerShell на запуск сценария нажмите клавишу R. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.</p>
</div>

    Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено приложение.

2.  В веб-браузере перейдите по адресу <http://localhost:8000/>, введите текст в поле **Добавить новую задачу** и нажмите **Добавить**.

3.  Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem.

4.  В файле app.js замените определение переменной `query` в методе **refreshTodoItems** на следующую строку кода:

        var query = todoItemTable.where({ complete: false }).take(3);

    При выполнении этого запроса будут возвращены три верхних элемента, которые не помечены как завершенные.

5.  Вернитесь к браузеру и перезагрузите страницу.

    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem.

6.  (Необязательно.) Просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, например средств разработчика браузера или Fiddler.

    Обратите внимание, что метод **take(3)** был преобразован в параметр запроса **$top=3** в URI запроса.

7.  Обновите запрос еще раз следующим кодом:

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Вернитесь к браузеру и перезагрузите страницу.

    Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике используется упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы <strong>Take</strong> и <strong>Skip</strong>. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице.  Вы можете также вызвать метод <strong>includeTotalCount</strong>, чтобы, помимо разбитых на страницы данных, получить общее количество элементов, доступных на сервере.</p>
</div>

9.  (Необязательно.) Снова просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу.

    Обратите внимание, что метод **skip(3)** был преобразован в параметр запроса **$skip=3** в URI запроса.

## <a name="next-steps"> </a> Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Далее узнайте, как проверить подлинность пользователей приложения, изучив учебник [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]. Дополнительные сведения об использовании мобильных служб с помощью HTML/JavaScript см. в разделе [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript].


  [Магазин Windows — C#]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [Xamarin.iOS]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client
