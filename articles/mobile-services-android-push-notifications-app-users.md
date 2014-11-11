<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Отправка push-уведомлений пользователям с использованием мобильных служб

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>Этот раздел расширяет <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-android">предыдущий учебник по push-уведомлениям</a>, добавляя новую таблицу для хранения универсальных кодов ресурса (URI) регистраций Google Cloud Messaging (GCM), которые затем можно использовать для отправки push-уведомлений нескольким пользователям приложения для Android. В этом учебнике одно обновление создает push-уведомления для всех зарегистрированных устройств при вставке данных в таблицу ToDoList. В предыдущем учебнике уведомление отправлялось только устройству, выполнявшему вставку.</p>
</div>

В этом учебнике рассматриваются следующие шаги по обновлению push-уведомлений в приложении:

1.  [Создание таблицы регистраций][Создание таблицы регистраций]
2.  [Обновление приложения][Обновление приложения]
3.  [Обновление серверных скриптов][Обновление серверных скриптов]
4.  [Проверка режима работы push-уведомлений][Проверка режима работы push-уведомлений]

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с push-уведомлениями][предыдущий учебник по push-уведомлениям]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с push-уведомлениями][предыдущий учебник по push-уведомлениям].

## <a name="create-table"></a>Создание новой таблицы

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][0]

2.  Щелкните вкладку **Данные**, а затем нажмите **Создать**.

    ![][1]

    Откроется диалоговое окно **Создание новой таблицы**.

3.  Сохранив параметр по умолчанию **Все с ключом приложения** для всех разрешений, введите *Registration* в поле **Имя таблицы** и нажмите кнопку «Проверить».

    ![][2]

    При этом создается таблица **Registration**, где отдельно от данных элементов хранятся идентификаторы URI регистрации, используемые для отправки push-уведомлений.

Далее следует изменить приложение push-уведомлений, чтобы данные регистрации сохранялись в этой новой таблице, а не в таблице **TodoItem**.

## <a name="update-app"></a>Обновление приложения

1.  В обозревателе пакетов щелкните правой кнопкой мыши пакет (в узле `src`), щелкните команду **Создать** и выберите **Класс**.

2.  В поле **Имя** введите `Registration`, затем щелкните **Готово**

    ![][3]

    При этом создается новый класс Registration.

3.  Откройте файл ToDoItem.java и вырежьте следующий код:

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  Вставьте вырезанный код в текст класса **Registration**, созданного ранее.

5.  Добавьте в класс **Registration** следующий код:

        @com.google.gson.annotations.SerializedName("id")
        private int mId;

        /**
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
         * 
         * @param id : id to set
         */
        public final void setId(int id) {
            mId = id;
        }

6.  Откройте файл **ToDoActivity.java** и удалите следующие строки из метода `addItem`:

        item.setHandle(MyHandler.getHandle());

7.  Найдите свойство `mClient` и замените его следующим кодом:

        /**
         * Mobile Service Client reference
         */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
         */
        public static MobileServiceClient getClient() {
            return mClient;
        }

8.  В файле **MyHandler** добавьте следующую инструкцию import:

        import android.util.Log;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  Добавьте в конец метода `onRegistered` следующий код:

        MobileServiceClient client = ToDoActivity.getClient();
        MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
            }
        });

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## <a name="update-scripts"></a>Обновление серверных сценариев

1.  На портале управления щелкните вкладку **Данные** и затем щелкните таблицу **Registration**.

    ![][4]

2.  В таблице **registration** щелкните вкладку **Сценарий** и выберите **Вставить**.

    ![][5]

    Это действие отображает функцию, которая вызывается при выполнении вставки в таблице **Registration**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registration');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertRegistrationIfNotFound });
            function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

    Этот сценарий проверяет таблицу **Registration** на наличие регистрации с таким же URI. Вставка выполняется только в том случае, если соответствующая регистрация отсутствует. Это предотвращает дублирование записей регистрации.

4.  Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**.

    ![][6]

5.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    sendNotifications(item.text);

                }
            });

        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }

    }

    Этот скрипт вставки отправляет push-уведомление (с текстом вставленного элемента) по всем регистрациям, хранящимся в таблице **Registration**.

## <a name="test-app"></a> Тестирование приложения

1.  В Eclipse в меню **Запуск** щелкните **Запуск**, чтобы запустить приложение.

2.  В приложении введите содержательный текст (например, *Новая задача для мобильных служб*, а затем нажмите кнопку **Добавить**.

3.  В нижней части экрана появится окно уведомления черного цвета.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с push-уведомлениями. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
   
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
   
    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
   
    Узнайте больше о регистрации и использовании серверных сценариев.

-   [Использование клиентской библиотеки Android для мобильных служб][Использование клиентской библиотеки Android для мобильных служб]
   
    Узнайте, как использовать мобильные службы с .NET.



  [предыдущий учебник по push-уведомлениям]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
  [Создание таблицы регистраций]: #create-table
  [Обновление приложения]: #update-app
  [Обновление серверных скриптов]: #update-scripts
  [Проверка режима работы push-уведомлений]: #test-app
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Использование клиентской библиотеки Android для мобильных служб]: /ru-ru/develop/mobile/how-to-guides/work-with-android-client-library
