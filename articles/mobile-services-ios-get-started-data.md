<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (iOS) | Центр разработчиков для мобильных устройств" metaKeywords="Данные Azure iOS, данные мобильных служб Azure, " description="Узнайте, как начать работы с мобильными службами для работы с данными в приложении iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>  
</div>	

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении iOS. В этом учебнике вам предстоит загрузить приложение, которое хранит данные в памяти, создать мобильную службу, интегрировать эту мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения iOS. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-ios">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения iOS] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

Для этого учебника требуется [Пакет Mobile Services iOS SDK] и [XCode 4.5][Установить Xcode], а также iOS версии 5.0 или более поздней версии.

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>
 
Этот учебник основан на приложении [GetStartedWithData][GitHub], которое является приложением iOS. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам iOS. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите GetStartedWithData [пример приложения][GitHub]. 

2. В Xcode откройте загруженный проект и просмотрите файл QSTodoService.m.

   	Обратите внимание на восемь комментариев **/ / TODO::**, указывающих на действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

3. Нажмите кнопку **Выполнить** (или сочетание клавиш Command + R) для повторного построения проекта и запуска приложения.

4. В приложении введите какой-либо текст в текстовом поле, а затем нажмите кнопку **+**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

1. Если пакет [iOS SDK для мобильных служб][Пакет Mobile Services iOS SDK] еще не установлен, установите его.

2. В навигаторе проекта в Xcode откройте файлы TodoService.m и TodoService.h, расположенные в папке Quickstart, и добавьте следующую инструкцию импорта: 

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3. В файле ToDoService.h найдите следующую закомментированную строку кода:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

   	После этого комментария добавьте следующую строку кода:

        @property (nonatomic, strong)   MSClient *client;

   	При этом создается свойство, которое представляет MSClient, который подключается к службе

4. В файле TodoService.m найдите следующую закомментированную строку кода:

        // Create an MSTable property for your items. 

   	После этого комментария добавьте следующую строку кода в объявлении @interface:

        @property (nonatomic, strong)   MSTable *table;

   	Это создает представление свойств таблицы мобильных служб.

5. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

6. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

7. В Xcode откройте TodoService.m и найдите следующую закомментированную строку кода:

        // Initialize the Mobile Service client with your URL and key.

    После этого комментария добавьте следующую строку кода:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Будет создан экземпляр клиента мобильных служб.

8. Замените значения **APPURL** и **APPKEY** в этом коде на URL-адрес и ключ приложения из мобильной службы, которые были получены на шаге 6.

9. Найдите следующую закомментированную строку кода:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    После этого комментария добавьте следующую строку кода:

        self.table = [self.client tableWithName:@"TodoItem"];

    Будет создан экземпляр таблицы TodoItem.

10. Найдите следующую закомментированную строку кода:

 	    // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    После этого комментария добавьте следующую строку кода:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    При этом создается запрос для возврата всех задач, которые еще не были завершены.

11. Найдите следующую закомментированную строку кода:

        // Query the TodoItem table and update the items property with the results from the service.

   	Замените этот комментарий и последующий блок вызова **completion** на следующий код:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

12. Найдите метод **addItem** и замените текст метода на следующий код:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
 
            // Let the caller know that we finished
            completion(index);
        }];

    Этот код отправляет в мобильную службу запрос на вставку.

13. Найдите метод **completeItem** и замените текст метода на следующий код:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
    
            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
    
            [mutableItems removeObjectAtIndex:index];
    
            // Let the caller know that we have finished
            completion(index);
	    }]; 

   	Этот код удаляет TodoItems после того, как они будут помечены как завершенные. 

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

1. В Xcode выберите эмулятор для развертывания (iPhone или iPad), нажмите кнопку **Выполнить** (или сочетание клавиш Command+R) для повторного построения проекта и запуска приложения.

   	Это приведет к выполнению клиента мобильных служб Azure, созданного с использованием iOS SDK, который запрашивает элементы из вашей мобильной службы.

2. Как и ранее, введите текст в текстовом поле и нажмите кнопку **+**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для iOS.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в iOS-приложении возможностей работы с данными в мобильных службах. 

Далее рекомендуется ознакомиться с одним из следующих учебников на основе приложения GetStartedWithData, созданного в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников iOS:

* [Приступая к работе с аутентификацией] 
	<br/>Сведения о выполнении проверки подлинности учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.

<!-- Anchors. -->
[Загрузка проекта приложения iOS]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios

[Портал управления Azure]: https://manage.windowsazure.com/
[Портале управления]: https://manage.windowsazure.com/
[Установить Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Пакет Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Репозиторий GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784

