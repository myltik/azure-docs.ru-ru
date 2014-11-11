<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Уточнение запросов мобильных служб c разбиением по страницам

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Версия C# Магазина Windows">Версия C# Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-js" title="Версия JavaScript Магазина Windows">Версия JavaScript Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению Xamarin.Android. В этом учебнике методы запроса **Take** и **Skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию &mdash; 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными][Приступая к работе с данными].

1.  В Xamarin Studio откройте проект, созданный после завершения учебника [Приступая к работе с данными][Приступая к работе с данными].

2.  Нажмите кнопку **Запуск**, чтобы запустить приложение, затем введите текст в текстовое поле и нажмите кнопку **Добавить**.

3.  Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem.

4.  В файле **TodoActivity.cs** замените запрос LINQ в методе **RefreshItemsFromTableAsync** следующим запросом:

        var list = await todoTable.Where(item => item.Complete == false)
                                  .Take(3)
                                  .ToListAsync();

    Этот запрос возвращает три верхних элемента, которые не помечены как завершенные.

5.  Повторно выполните сборку и запустите приложение.

    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem.

6.  (Необязательно.) Просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, например средств разработчика браузера или Fiddler.

    Обратите внимание, что в универсальном коде ресурса (URI) запроса метод `Take(3)` был преобразован в параметр запроса `$top=3`.

7.  Еще раз обновите запрос LINQ в методе **RefreshItemsFromTableAsync** с помощью следующего запроса:

            var list = await todoTable.Where(item => item.Complete == false)
                                      .Skip(3)
                                      .Take(3)
                                      .ToListAsync();

    Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике используется упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы <strong>Take</strong> и <strong>Skip</strong>. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Можно также вызвать метод <strong>IncludeTotalCount</strong>, чтобы, помимо разбитых на страницы данных, получить общее количество элементов, доступных на сервере.</p>
</div>

8.  (Необязательно.) Снова просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу.

    Обратите внимание, что в универсальном коде ресурса (URI) запроса метод `Skip(3)` был преобразован в параметр запроса `$skip=3`.

## <a name="next-steps"> </a> Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
