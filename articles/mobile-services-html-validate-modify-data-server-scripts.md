<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Проверка данных — HTML5" pageTitle="Использование серверных скриптов для проверки и изменения данных (HTML 5) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как проверять и изменять данные с помощью серверных скриптов в приложении HTML." metaCanonical="" services="" documentationCenter="Mobile" title="Проверяйте и изменяйте данные с помощью серверных скриптов в мобильных службах" authors="glenga" solutions="" manager="" editor="" />




# Проверка и изменение данных в мобильных службах с помощью серверных скриптов 
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение HTML, чтобы воспользоваться преимуществом этого нового поведения.

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление отметки времени при вставке]
4. [Обновление клиента для отображения отметки времени]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинные строки; в этом случае отклоняются строки длиной более 10 символов.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните элемент **Скрипт** и выберите операцию **Вставить**.

   	![][2]

4. Замените имеющийся скрип следующей функцией и нажмите кнопку **Сохранить**.

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
	<b>Примечание</b> 
	<p>Можно удалить зарегистрированный скрипт на вкладке <strong>Скрипт</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>	

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. Выполните один из следующих командных файлов из вложенной папки **сервер** проекта, которые вы изменили после завершения учебника [Приступая к работе с данными].

	+ **launch-windows** (компьютеры Windows) 
	+ **launch-mac.command** (компьютеры Mac OS X)
	+ **launch-linux.sh** (компьютеры Linux)

	<div class="dev-callout"><b>Примечание.</b>
		<p>На компьютере Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск скрипта. Веб-браузер может предупредить о том, что не следует запускать этот скрипт, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку скрипта.</p>
	</div>

	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено приложение.

1. 	Откройте файл app.js, а затем замените обработчик событий **$('#add-item').submit()** следующим кодом:

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

2. В веб-браузере перейдите к <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, введите текст в поле **Добавить задачу** и щелкните **Добавить**.

   	Обратите внимание, что операция завершается неудачей и в диалоговом окне отображается ответ с сообщением об ошибке.

## <a name="add-timestamp"></a>Добавление отметки времени

Предыдущие задачи проверили вставку и приняли или отклонили ее. Теперь можно обновить вставленные данные путем использования серверного скрипта, который добавляет свойство отметки времени в объект до его вставки.

<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство отметки времени <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы.</p>
</div>

1. На вкладке **Скрипты** [портала управления] замените текущий скрипт **Вставить** на следующую функцию, затем нажмите **Сохранить**.

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
	<p>При первом выполнении этого скрипта вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения ее следует отключить.</p>
    </div>

2. В веб-браузере перезагрузите страницу, а затем введите текст (менее 10 символов) в поле **Добавить задачу** и нажмите кнопку **Добавить**.

   	Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3. На портале управления щелкните вкладку **Обзор** в таблице **todoitem**.
   
   	Обратите внимание на появление столбца **createdAt** и на то, что новый вставленный элемент имеет значение отметки времени.
  
Далее необходимо обновить приложение для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Повторное обновление клиента

Клиент мобильной службы игнорирует любые данные в ответе, которые не могут быть сериализованы в свойства определенного типа. Последним шагом является обновление клиента для отображения новых данных.

1. В редакторе откройте файл app.js, а затем замените функцию **refreshTodoItems** следующим кодом:

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

2. В редакторе откройте файл style.css и замените стили класса `item-text` следующим:

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	В результате изменятся размеры текстового поля и стили нового текста метки.
	
6. Перезагрузите страницу. 	

   	Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

7. Снова в функции **refreshTodoItems** замените строку кода, которая определяет запрос, следующим:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	Эта функция обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.
	
8. Перезагрузите страницу.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с этим учебником по работе с данными.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов посредством разбиения по страницам].

Дополнительные сведения см. в разделах [Работа с серверными скриптами в мобильных службах] и [Справочник принципов использования мобильных служб HTML/JavaScript]


<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление отметки времени при вставке]: #add-timestamp
[Обновление клиента для отображения отметки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Работа с серверными скриптами]: /ru-ru/develop/mobile/how-to-guides/work-with-server-scripts
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-html
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-html
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-html
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client

