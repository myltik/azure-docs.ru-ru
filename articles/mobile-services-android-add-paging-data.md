<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Уточнение запросов мобильных служб c разбиением по страницам

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению Android. В этом учебнике методы запроса **top** и **skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию &mdash; 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными][Приступая к работе с данными].

1.  В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с данными][Приступая к работе с данными].

2.  В меню **Запуск** нажмите кнопку **Запуск** для запуска приложения, затем введите текст в текстовое поле и нажмите кнопку **Добавить**.

3.  Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem.

4.  В файле ToDoActivity.java замените метод **RefreshTodoItems** следующим кодом:

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Этот запрос возвращает три верхних элемента, которые не помечены как завершенные.

5.  Повторно выполните сборку и запустите приложение.

    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem.

6.  (Необязательно.) Просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, например средств разработчика браузера или Fiddler.

    Обратите внимание, что в универсальном коде ресурса (URI) запроса метод `top(3)` был преобразован в параметр запроса `$top=3`.

7.  Обновите метод **RefreshTodoItems** еще раз следующим кодом:

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).skip(3).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике используется упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы <strong>top</strong> и <strong>skip</strong>. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Можно также вызвать метод <strong>IncludeTotalCount</strong>, чтобы, помимо разбитых на страницы данных, получить общее количество элементов, доступных на сервере.</p>
</div>

8.  (Необязательно.) Снова просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу.

    Обратите внимание, что в универсальном коде ресурса (URI) запроса метод `skip(3)` был преобразован в параметр запроса `$skip=3`.

## <a name="next-steps"> </a> Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
   
    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
   
    Сведения об отправке в приложение простейших push-уведомлений.



  [Магазин Windows — C#]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [Xamarin.iOS]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
