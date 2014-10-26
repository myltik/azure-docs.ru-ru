<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-android" urlDisplayName="Validate Data - Android" pageTitle="Use server scripts to validate and modify data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение Android, чтобы воспользоваться преимуществом этого нового поведения.

В этом учебнике рассматриваются следующие основные действия:

1.  [Добавление проверки длины строки][Добавление проверки длины строки]
2.  [Обновление клиента для поддержки проверки][Обновление клиента для поддержки проверки]
3.  [Добавление отметки времени при вставке][Добавление отметки времени при вставке]
4.  [Обновление клиента для отображения отметки времени][Обновление клиента для отображения отметки времени]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными][Приступая к работе с данными].

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][]

2.  Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

    ![][1]

3.  Щелкните **Сценарий** и выберите операцию **Вставить**.

    ![][2]

4.  Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Этот скрипт проверяет длину свойства **text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    <div class="dev-callout"> 
<b>Примечание.</b> 
<p>Можно удалить зарегистрированный сценарий на вкладке <strong>Сценарий</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо проверить, правильно ли приложение обрабатывает сообщения об ошибках, полученные при выполнении проверки.

1.  В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с данными][Приступая к работе с данными].

2.  В файле ToDoActivity.java найдите метод **addItem** и замените вызов метода createAndShowDialog следующим кодом:

        createAndShowDialog(exception.getCause().getMessage(), "Error");

    Отобразится сообщение об ошибке, возвращенное мобильной службой.

3.  В меню **Запуск** нажмите кнопку **Запуск** для запуска приложения, затем введите текст длиннее 10 символов в текстовое поле и нажмите кнопку **Добавить**.

    Обратите внимание, что ошибка будет обработана и для пользователя отобразится сообщение об ошибке.

## <a name="add-timestamp"></a>Добавление отметки времени

Предыдущие задачи проверили вставку и приняли или отклонили ее. Теперь можно обновить вставленные данные путем использования серверного скрипта, который добавляет свойство отметки времени в объект до его вставки.

<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство метки времени <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы.</p>
</div>

1.  На вкладке **Сценарии** [портала управления][портал управления Azure] замените текущий сценарий **вставки** следующей функцией, затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция расширяет предыдущий скрипт вставки путем добавления нового свойства отметки времени **createdAt** в объект до его вставки путем вызова **request**.**execute**.

    <div class="dev-callout"><b>Примечание.</b>
<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения ее следует отключить.</p>
</div>

2.  В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение, затем введите текст не больше 10 символов в текстовое поле и нажмите кнопку **Добавить**.

    Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3.  На портале управления щелкните вкладку **Обзор** в таблице **todoitem**.

    Обратите внимание на появление столбца **createdAt** и на то, что новый вставленный элемент имеет значение отметки времени.

Далее необходимо обновить приложение Android для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Повторное обновление клиента

Клиент мобильной службы игнорирует любые данные в ответе, которые не могут быть сериализованы в свойства определенного типа. Последним шагом является обновление клиента для отображения новых данных.

1.  В обозревателе пакетов откройте файл ToDoItem.java и добавьте следующую инструкцию **import**:

        import java.util.Date;

2.  Добавьте следующий код в определения частных полей в классе **TodoItem**:

        /**
         * Timestamp of the item inserted by the service.
         */
        @com.google.gson.annotations.SerializedName("createdAt")
        private Date mCreatedAt;

    <div class="dev-callout"><b>Примечание.</b>
<p>Заметка <code data-inline="1">SerializedName</code> вынуждает клиент сопоставить новое свойство <code data-inline="1">mCreatedAt</code> в приложении со столбцом <code data-inline="1">createdAt</code>, определенным в таблице TodoItem с другим именем. С помощью этой заметки ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL. Без этой заметки возникает ошибка из-за различия в буквенном регистре.</p>
</div>

3.  Добавьте следующие методы в класс ToDoItem, чтобы получить и установить новое свойство mCreatedAt:

        /**
         * Sets the timestamp.
         * 
         * @param date
         *            timestamp to set
         */
        public final void setCreatedAt(Date date) {
            mCreatedAt = date;
        }

        /**
         * Returns the timestamp.
         */
        public Date getCreatedAt() {
            return mCreatedAt;
        }

4.  В обозревателе пакетов откройте файл ToDoItemAdapter.java и добавьте следующую инструкцию **import**:

        import java.text.DateFormat;

5.  В методе GetView добавьте следующий код:

        String createdAtText = "";
        if (currentItem.getCreatedAt() != null){
            DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
            createdAtText = formatter.format(currentItem.getCreatedAt());
        }

    При этом создается форматированная строка даты, если существует значение отметки времени.

6.  Найдите код `checkBox.setText(currentItem.getText());` и замените эту строку следующей:

        checkBox.setText(currentItem.getText() + " " + createdAtText);

    При этом к элементу для отображения добавляется метка времени.

7.  В меню **Выполнить** щелкните **Выполнить** для запуска приложения.

    Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

8.  Замените существующий метод **RefreshItemsFromTable** следующим кодом:

        private void refreshItemsFromTable() {

            mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
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

    Этот метод обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.

9.  В меню **Выполнить** щелкните **Выполнить** для запуска приложения.

    Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов c разбиением по страницам][Уточнение запросов c разбиением по страницам].

Серверные скрипты используются также при авторизации пользователей для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

-   [Авторизация пользователей с помощью сценариев][Авторизация пользователей с помощью сценариев]

    Узнайте, как фильтровать данные на основании идентификатора аутентифицированного пользователя.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
   
    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]

    Узнайте больше о регистрации и использовании серверных сценариев.



  [Магазин Windows — C#]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Добавление проверки длины строки]: #string-length-validation
  [Обновление клиента для поддержки проверки]: #update-client-validation
  [Добавление отметки времени при вставке]: #add-timestamp
  [Обновление клиента для отображения отметки времени]: #update-client-timestamp
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-android
  [Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
