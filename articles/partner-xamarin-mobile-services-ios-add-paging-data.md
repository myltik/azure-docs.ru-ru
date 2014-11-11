<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Уточнение запросов мобильных служб c разбиением по страницам

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Версия C# Магазина Windows">Версия C# Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-js" title="Версия JavaScript Магазина Windows">Версия JavaScript Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению iOS. В этом учебнике свойства запроса **Skip** и **Take** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию &mdash; 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными][Приступая к работе с данными].

1.  Откройте в Xamarin Studio проект, измененный при завершении изучения учебника [Приступая к работе с данными][Приступая к работе с данными].

2.  Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта и запустить приложение, а затем введите текст в текстовое поле и нажмите значок «плюс» (**+**).

3.  Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem.

4.  Откройте файл **TodoService.cs**, найдите метод **RefreshDataAsync** и замените запрос LINQ на `todoTable` следующим запросом:

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

    Этот запрос возвращает три верхних элемента, которые не помечены как завершенные.

5.  Повторно выполните сборку и запустите приложение.

    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem.

6.  Обновите запрос LINQ в методе **RefreshDataAsync** еще раз, как указано далее:

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Skip(3)
                            .Take(3)
                            .ToListAsync();

    На этот раз установите значение **Skip** равным 3.

    Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике используется упрощенный сценарий, где для свойств <strong>Skip</strong> и <strong>Take</strong> задаются жестко запрограммированные значения разбиения по страницам. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Можно также вызвать метод <strong>IncludeTotalCount</strong>, чтобы, помимо разделенных по страницам данных, получить общее количество элементов, доступных на сервере.</p>
</div>

## <a name="next-steps"> </a> Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    <br/>Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

<!-- 
* [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. 
--> 
<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
