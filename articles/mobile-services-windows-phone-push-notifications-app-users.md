<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Отправка push-уведомлений пользователям с использованием мобильных служб

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

Этот раздел является расширением [предыдущего учебника по push-уведомлениям][предыдущего учебника по push-уведомлениям] и добавляет новую таблицу для хранения URI каналов службы push-уведомлений Майкрософт (MPNS). Эти каналы можно использовать для отправки push-уведомлений пользователям приложения Windows Phone 8.

В этом учебнике рассматриваются следующие шаги по обновлению push-уведомлений в приложении:

1.  [Создание таблицы Channel][Создание таблицы Channel]
2.  [Обновление приложения][Обновление приложения]
3.  [Обновление серверных скриптов][Обновление серверных скриптов]
4.  [Проверка режима работы push-уведомлений][Проверка режима работы push-уведомлений]

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с push-уведомлениями][предыдущего учебника по push-уведомлениям]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с push-уведомлениями][предыдущего учебника по push-уведомлениям].

## <a name="create-table"></a>Создание новой таблицы

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][0]

2.  Щелкните вкладку **Данные**, а затем нажмите **Создать**.

    ![][1]

    Откроется диалоговое окно **Создание новой таблицы**.

3.  Не убирая параметр по умолчанию **Все с ключом приложения** для всех разрешений, введите *Channel* в поле **Имя таблицы** и нажмите кнопку «Проверить».

    ![][2]

    При этом создается таблица **Channel**, где отдельно от данных элементов хранятся идентификаторы URI, используемые для отправки push-уведомлений.

Далее следует изменить приложение push-уведомлений, чтобы данные сохранялись в этой новой таблице, а не в таблице **TodoItem**.

## <a name="update-app"></a>Обновление приложения

1.  Откройте в Visual Studio 2012 Express для Windows Phone проект из учебника [Приступая к работе с push-уведомлениями][предыдущего учебника по push-уведомлениям], откройте файл MainPage.xaml.cs и удалите свойство **Channel** и класса **TodoItem**. Теперь он должен выглядеть следующим образом:

        public class TodoItem
        {
            public int Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

2.  Замените метод обработчик событий **ButtonSave\_Click** на исходную версию этого метода, как показано ниже:

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3.  Добавьте следующий код, который создает новый класс **Channel**:

        public class Channel
        {
            public int Id { get; set; }
            [JsonProperty(PropertyName = "uri")]
            public string Uri { get; set; }
        }

4.  Откройте файл App.xaml.cs и замените метод **AcquirePushChannel** следующим кодом:

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

## <a name="update-scripts"></a>Обновление серверных сценариев

1.  На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **Channel**.

    ![][3]

2.  В таблице **Channel** щелкните вкладку **Сценарий** и выберите **Вставка**.

    ![][4]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **Channel**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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

4.  Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**.

    ![][5]

5.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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

## <a name="test-app"></a> Тестирование приложения

1.  В Visual Studio выберите **Развернуть решение** в меню **Сборка**.

2.  Коснитесь плитки с именем **TodoList** или **hello push**, чтобы запустить приложение.

    ![][6]

3.  В приложении введите в текстовом поле текст «hello push again» и нажмите кнопку **Сохранить**.

    ![][7]

    При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента.

4.  Нажмите клавишу **Пуск**, чтобы вернуться в меню "Пуск".

    ![][8]

    Обратите внимание, что приложение получило push-уведомление и теперь на плитке отображается текст **Первое push-уведомление**.

5.  (Необязательно.) Запустите приложение одновременно на двух устройствах и повторите предыдущий шаг.

    Уведомление отправляется во все запущенные экземпляры приложения.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с push-уведомлениями. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    <br/>Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    <br/>Узнайте больше о регистрации и использовании серверных сценариев.

<!-- anchors -->
<!-- Images. -->
<!-- URLs. -->

  [предыдущего учебника по push-уведомлениям]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
  [Создание таблицы Channel]: #create-table
  [Обновление приложения]: #update-app
  [Обновление серверных скриптов]: #update-scripts
  [Проверка режима работы push-уведомлений]: #test-app
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
  [3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
  [4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
  [5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
  [7]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
  [8]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
