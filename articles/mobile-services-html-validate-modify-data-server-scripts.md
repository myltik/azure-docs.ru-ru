<properties urlDisplayName="Validate Data - HTML5" pageTitle="Использование серверных скриптов для проверки и изменения данных (HTML 5) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как проверять и изменять данные, отправленные с помощью серверных сценариев из приложения HTML." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов 

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Поскольку поведение скриптов на стороне сервера часто влияет на клиентскую сторону, также потребуется обновить ваше HTML-приложение, чтобы воспользоваться преимуществами нового поведения.

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление отметки времени при вставке]
4. [Обновление клиента для отображения отметки времени]

В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Прежде чем начать работу с этим учебником, необходимо пройти [Начало работы с данными].  

## <a name="string-length-validation"></a>Добавить проверку

Всегда рекомендуется проверять длину данных, отправляемых пользователям. Вначале регистрируется скрипт для проверки значений длины данных строк, отправляемых мобильной службе, и удаления слишком длинных строк, в данном случае - превышающих 10 символов.

1. Зарегистрируйтесь на [Портале управления Azure], щелкните **Мобильные службы**, затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните **Скрипт**, затем выберите операцию **Вставить**.

   	![][2]

4. Замените существующий скрипт следующей функцией, затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Этот сценарий проверяет длину свойства **TodoItem.text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается функция **Выполнить** для завершения ввода.

    <div class="dev-callout"> 
	<b>Примечание.</b> 
	<p>Можно удалить зарегистрированный скрипт на вкладке <strong>Скрипт</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>	

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. Выполните один из следующих командных файлов из вложенной папки **server** проекта, которые вы изменили после завершения учебника [Приступая к работе с данными].

	+ **launch-windows** (компьютеры с ОС Windows) 
	+ **launch-mac.command** (компьютеры с ОС Mac OS X)
	+ **launch-linux.sh** (компьютеры с ОС Linux)

	<div class="dev-callout"><b>Примечание.</b>
		<p>На компьютере Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск скрипта. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку скрипта.</p>
	</div>

	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено приложение.

1. 	Откройте файл app.js, затем замените обработчик событий **$('#add-item').submit()** следующим кодом:

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

2. В веб-браузере откройте адрес <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, введите текст в поле **Добавить задачу** и нажмите **Добавить**.

   	Обратите внимание, что операция завершается неудачей и в диалоговом окне отображается ответ с сообщением об ошибке.

## <a name="add-timestamp"></a>Добавление временной метки

В предыдущих задачах проверялась операция вставки, после чего была принята или отклонена. Теперь будет проводиться обновление вставленных данных с использованием серверного скрипта, который добавляет свойство временной метки к объекту перед его вставкой.

<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство отметки времени <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы.</p>
</div>

1. На вкладке **Скрипты** на [Портале управления] замените текущий скрипт **Вставка** следующей функцией, затем щелкните ** Сохранить**.

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

    Эта функция дополняет предыдущий скрипт вставки, добавляя новое свойство временной метки **createdAt** к объекту перед его вставкой путем вызова **request**.**execute**. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого скрипта вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения ее следует отключить.</p>
    </div>

2. Перезагрузите страницу в веб-браузере, затем введите текст (менее 10 символов) в окне **Добавить новое задание** и щелкните **Добавить**.

   	Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3. Возвратившись на портал управления, откройте вкладку **Обзор** в таблице **todoitem**.
   
   	Обратите внимание, что теперь появился столбец **createdAt**, а вновь вставленный элемент имеет значение временной метки.
  
Далее необходимо обновить приложение для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Снова обновите клиент

Клиент мобильной службы будет пропускать любые данные в ответе, которые не сможет сериализовать в свойства согласно заданному типу. Последним шагом является обновление клиента для отображения этих новых данных.

1. Откройте в редакторе файл app.js, затем замените функцию **refreshTodoItems** следующим кодом:

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

   	Это отображает часть нового свойства **createdAt**, отвечающую за дату. 

2. Откройте в редакторе файл style.css и замените стили в классе `item-text` следующим:

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	В результате изменятся размеры текстового поля и стили нового текста метки.
	
6. Перезагрузите страницу. 	

   	Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

7. Еще раз замените строку кода, определяющую запрос в функции **refreshTodoItems**, следующим:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	Эта функция обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.
	
8. Перезагрузите страницу.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром].

Для получения дополнительной информации см. разделы [Работа с серверными скриптами] и [Мобильные службы HTML/JavaScript, как создавать концептуальные ссылки]


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
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client
