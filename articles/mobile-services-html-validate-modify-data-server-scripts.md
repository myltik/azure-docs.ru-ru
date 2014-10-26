<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Validate Data - HTML5" pageTitle="User server scripts to validate and modify data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение HTML, чтобы воспользоваться преимуществом этого нового поведения.

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
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Этот скрипт проверяет длину свойства **TodoItem.text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается функция **execute** для завершения вставки.

    <div class="dev-callout"> 
<b>Примечание.</b> 
<p>Можно удалить зарегистрированный сценарий на вкладке <strong>Сценарий</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1.  Запустите один из следующих командных файлов во вложенной папке **server** проекта, измененного в ходе изучения материала учебника [Приступая к работе с данными][Приступая к работе с данными].

    -   **launch-windows** (компьютеры с ОС Windows)
    -   **launch-mac.command** (компьютеры с ОС Mac OS X)
    -   **launch-linux.sh** (компьютеры с ОС Linux)

    <div class="dev-callout"><b>Примечание.</b>
    <p>На компьютере с операционной системой Windows в ответ на запрос PowerShell на запуск сценария нажмите клавишу R. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.</p>
</div>

    Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено приложение.

2.  Откройте файл app.js, а затем замените обработчик событий **$('\#add-item').submit()** следующим кодом:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems, function(error){
                    alert(JSON.parse(error.request.responseText).error);
                });
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

3.  В веб-браузере перейдите по адресу <http://localhost:8000/>, введите текст в поле **Добавить новую задачу** и нажмите **Добавить**.

    Обратите внимание, что операция завершается неудачей и в диалоговом окне отображается ответ с сообщением об ошибке.

## <a name="add-timestamp"></a>Добавление отметки времени

Предыдущие задачи проверили вставку и приняли или отклонили ее. Теперь можно обновить вставленные данные путем использования серверного скрипта, который добавляет свойство отметки времени в объект до его вставки.

<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство метки времени <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы.</p>
</div>

1.  На вкладке **Сценарии** [портала управления][портал управления Azure] замените текущий сценарий **вставки** следующей функцией, затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: 'Text length must be under 10'
                });
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция расширяет предыдущий скрипт вставки путем добавления нового свойства отметки времени **createdAt** в объект до его вставки путем вызова **request**.**execute**.

    <div class="dev-callout"><b>Примечание.</b>
<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения ее следует отключить.</p>
</div>

2.  В браузере перезагрузите страницу, затем в поле **Добавить новую задачу** введите текст (менее 10 символов) и нажмите кнопку **Добавить**.

    Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3.  На портале управления щелкните вкладку **Обзор** в таблице **todoitem**.

    Обратите внимание на появление столбца **createdAt** и на то, что новый вставленный элемент имеет значение отметки времени.

Далее необходимо обновить приложение для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Повторное обновление клиента

Клиент мобильной службы игнорирует любые данные в ответе, которые не могут быть сериализованы в свойства определенного типа. Последним шагом является обновление клиента для отображения новых данных.

1.  В редакторе откройте файл app.js и замените функцию **refreshTodoItems** следующим кодом:

        function refreshTodoItems() {
            var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });
            query.read().then(function(todoItems) {
                var listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">')
                            .prop('checked', item.complete))
                        .append($('<div>').append($('<input class="item-text">').val(item.text))
                        .append($('<span class="timestamp">' 
                            + (item.createdAt && item.createdAt.toDateString() + ' '
                            + item.createdAt.toLocaleTimeString() || '') 
                            + '</span>')));
                });
                $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
            });
        }

    Это приведет к отображению раздела даты в новом свойстве **createdAt**.

2.  В редакторе откройте файл style.css и замените стили класса `item-text` следующим кодом:

        .item-text { width: 70%; height: 26px; line-height: 24px; 
            border: 1px solid transparent; background-color: transparent; }
        .timestamp { width: 30%; height: 40px; font-size: .75em; }

    В результате изменятся размеры текстового поля и стили нового текста метки.

3.  Перезагрузите страницу.

    Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

4.  В функции **refreshTodoItems** снова замените строку кода, которая определяет запрос, следующим:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

    Эта функция обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.

5.  Перезагрузите страницу.

    Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов c разбиением по страницам][Уточнение запросов c разбиением по страницам].

Дополнительные сведения см. в разделах [Работа с серверными скриптами в мобильных службах][Работа с серверными скриптами в мобильных службах] и [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]



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
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-html
  [Работа с серверными скриптами в мобильных службах]: /ru-ru/develop/mobile/how-to-guides/work-with-server-scripts
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client
