<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Уточнение запросов мобильных служб c разбиением по страницам

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению iOS. В этом учебнике свойства запроса **fetchLimit** и **fetchOffset** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию &mdash; 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными][Приступая к работе с данными].

1.  В Xcode откройте проект, измененный после завершения учебника [Приступая к работе с данными][Приступая к работе с данными].

2.  Нажмите кнопку **Запуск** (или клавиши Command+R), чтобы выполнить сборку проекта и запустить приложение, а затем введите текст в текстовом поле и нажмите значок «плюс» (**+**).

3.  Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem.

4.  Откройте файл QSTodoService.m и найдите следующий метод:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

    Замените существующий метод целиком на следующий код.

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];
        query.includeTotalCount = YES; // Request the total item count
        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;
        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {
            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
            }
            items = [results mutableCopy];
            // Let the caller know that we finished
            completion();
        }];

    Этот запрос возвращает три верхних элемента, которые не помечены как завершенные.

5.  Повторно выполните сборку и запустите приложение.

    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem.

6.  Еще раз обновите метод **refreshDataOnSuccess**, найдя следующую строку кода:

        query.fetchOffset = 0;

    На этот раз установите значение **query.fetchOffset** равным 3.

    Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике используется упрощенный сценарий, в котором для свойств <strong>fetchOffset</strong> и <strong>fetchLimit</strong> задаются жестко запрограммированные значения разбиения по страницам. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Вы также можете установить значение **query.includeTotalCount = YES**, чтобы вместе с разбитыми на страницы данными получить общее количество элементов, доступных на сервере.</p>
</div>

## <a name="next-steps"> </a> Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]    
    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.



  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
