<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push-уведомления для пользователей (WP8)" pageTitle="Push-уведомления для пользователей (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как использовать мобильные службы для отправки push-уведомлений пользователям вашего приложения Windows Phone." metaCanonical="" services="" documentationCenter="" title="Отправка push-уведомлений пользователям с использованием мобильных служб" authors="glenga" solutions="" manager="" editor="" />




# Отправка push-уведомлений пользователям с использованием мобильных служб
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>


Этот раздел является расширением [предыдущего учебника по push-уведомлениям][Get started with push notifications] и добавляет новую таблицу для хранения URI каналов службы push-уведомлений Майкрософт (MPNS). Эти каналы можно использовать для отправки push-уведомлений пользователям приложения Windows Phone 8.  

В этом учебнике рассматриваются следующие шаги по обновлению push-уведомлений в приложении:

1. [Создание таблицы Channel]
2. [Обновление приложения]
3. [Обновление серверных скриптов]
4. [Проверка режима работы push-уведомлений] 

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с push-уведомлениями]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с push-уведомлениями].  

## <a name="create-table"></a>Создание таблицы

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

   	![][0]

2. Щелкните вкладку **Данные**, а затем нажмите **Создать**.

   	![][1]

   	Откроется диалоговое окно **Создание новой таблицы**.

3. Сохранив параметр по умолчанию **Все с ключом приложения** для всех разрешений, введите _Channel_ в поле **Имя таблицы** и нажмите кнопку "Проверить".

   	![][2]

  	При этом создается таблица **Channel**, где отдельно от данных элементов хранятся идентификаторы URI, используемые для отправки push-уведомлений.

Далее следует изменить приложение push-уведомлений, чтобы данные сохранялись в этой новой таблице, а не в таблице **TodoItem**.

## <a name="update-app"></a>Обновление приложения

1. Откройте в Visual Studio 2012 Express для Windows Phone проект из учебника [Приступая к работе с push-уведомлениями], откройте файл MainPage.xaml.cs и удалите свойство **Channel** из класса **TodoItem**. Теперь он должен выглядеть следующим образом:

        public class TodoItem
        {
        	public int Id { get; set; }

	       	[JsonProperty(PropertyName = "text")]
	        public string Text { get; set; }
	
	        [JsonProperty(PropertyName = "complete")]
	        public bool Complete { get; set; }
        }

2. Заменить метод обработчика событий **ButtonSave_Click** на исходную версию этого метода, как показано ниже:

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3. Добавьте следующий код, который создает новый класс **Channel**:

	    public class Channel
	    {
	        public int Id { get; set; }
	
	        [JsonProperty(PropertyName = "uri")]
	        public string Uri { get; set; }
	    }

4. Откройте файл App.xaml.cs и замените метод **AcquirePushChannel** следующим кодом:

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

     Этот код вставляет новый канал в таблицу Channel.

## <a name="update-scripts"></a>Обновление серверных скриптов

1. На портале управления откройте вкладку **Данные** и затем щелкните таблицу **Channel**. 

   	![][3]

2. В таблице **channel** откройте вкладку **Скрипт** и выберите элемент **Вставить**.
   
   	![][4]

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **Channel**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

			function insert(item, user, request) {
				var channelTable = tables.getTable('Channel');
				channelTable
					.where({ uri: item.uri })
					.read({ success: insertChannelIfNotFound });
		        function insertChannelIfNotFound(existingChannels) {
	        	    if (existingChannels.length > 0) {
	            	    request.respond(200, existingChannels[0]);
	        	    } else {
	            	    request.execute();
	        	    }
	    	    }
		    }

   	Этот сценарий проверяет таблицу **Channel** на наличие канала с таким же URI. Вставка выполняется только в том случае, если соответствующий канал отсутствует. Это предотвращает дублирование записей канала.

4. Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**. 

   	![][5]

5. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var channelTable = tables.getTable('Channel');
        	    channelTable.read({
            	    success: function(channels) {
                	    channels.forEach(function(channel) {
                    	    push.mpns.sendFlipTile(channel.uri, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Этот скрипт вставки отправляет push-уведомление (с текстом вставленного элемента) по всем каналам, хранящимся в таблице **Channel**.

## <a name="test-app"></a>Тестирование приложения

1. В Visual Studio выберите **Развернуть решение** в меню **Сборка**.

3. Коснитесь плитки с именем **TodoList** или **hello push** для запуска приложения. 

  	![][23]

5. В приложении введите в текстовом поле текст "hello push again" и нажмите кнопку **Сохранить**.

   	![][24]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента.

6. Нажмите кнопку **Запуск**, чтобы вернуться в главное меню. 

  	![][25]

  	Обратите внимание, что приложение получило push-уведомление и теперь на плитке отображается текст **Первое push-уведомление**.

9. (Необязательно) Запустите приложение одновременно на двух устройствах и повторите предыдущий шаг. 

    Уведомление отправляется во все запущенные экземпляры приложения.  

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с push-уведомлениями. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

<!-- anchors -->
[Создание таблицы Channel]: #create-table
[Обновление приложения]: #update-app
[Обновление серверных скриптов]: #update-scripts
[Проверка режима работы push-уведомлений]: #test-app
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
[3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
[4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
[5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png


[23]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
[24]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
[25]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления Azure]: https://manage.windowsazure.com/

