<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Добавление разбиения по страницам в данные" pageTitle="Добавление разбиения по страницам в данные (iOS) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как использовать разбиение по страницам для управления объемом данных, возвращаемых в ваше приложение iOS из мобильных служб." metaCanonical="" services="" documentationCenter="Mobile" title="Уточнение запросов мобильных служб посредством разбиения по страницам" authors="" solutions="" manager="" editor="" />




# Уточнение запросов мобильных служб посредством разбиения по страницам

> [AZURE.SELECTOR-LIST (платформа | сервер)]
- [(Магазин Windows C# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
- [(Магазин Windows C# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
- [(Магазин Windows JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
- [(Магазин Windows JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
- [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
- [(Android | JavaScript)](mobile-services-android-add-paging-data)
- [(HTML | .NET)](mobile-services-html-add-paging-data)
- [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
- [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)
 

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению iOS. В этом учебнике свойства запроса **fetchLimit** и **fetchOffset** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию — 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными]. 

1. В Xcode откройте проект, измененный после завершения учебника [Приступая к работе с данными].

2. Нажмите кнопку **Запуск** (Command + R), чтобы построить проект и запустить приложение, а затем введите текст в текстовом поле и нажмите значок плюса (**+**) .

3. Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem. 

4. Откройте файл QSTodoService.m и найдите следующий метод:

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

5. Повторно выполните построение и запустите приложение. 
   
    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem. 

7. Обновите метод **refreshDataOnSuccess** еще раз, найдя следующую строку кода:

        query.fetchOffset = 0;

   	На этот раз установите значение **query.fetchOffset** равным 3. 

   	Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая «страница» данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
    <p>В этом учебнике используется упрощенный сценарий установки жестко запрограммированных значений разбиения по страницам для свойств <strong>fetchOffset</strong> и <strong>fetchLimit</strong>. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Можно также установить значение **query.includeTotalCount = YES**, чтобы, помимо разбитых на страницы данных, получить общее количество элементов, доступных на сервере.</p>
    </div>

## <a name="next-steps"> </a>Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.
 
<!--
* [Get started with push notifications] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.
-->

<!-- Anchors. -->

[Следующие шаги]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios

[Портал управления]: https://manage.windowsazure.com/

