<properties urlDisplayName="Call a custom API from the client" pageTitle="Вызов настраиваемого API из HTML-клиента. Мобильные службы" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Вызов настраиваемого API из приложения HTM

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать пользовательский API из приложения HTML. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. Настраиваемый API позволяет получить больший контроль над передачей сообщений, включая чтение и задание заголовков сообщений HTTP и определение формата текста сообщения, отличного от JSON.

Пользовательский API, созданный в этом разделе, позволяет отправить единственный запрос POST, который устанавливает флаг completed равным `true` для всех подлежащих выполнению элементов в таблице. Без этого настраиваемого API клиенту пришлось бы отправлять отдельные запросы для обновления в таблице каждого элемента, подлежащего выполнению.

Добавьте эту функциональную возможность в приложение, созданное при прохождении учебника [Начало работы с мобильными службами] или [Начало работы с данными]. Для этого выполните следующие шаги:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого API]
3. [Тестирование приложения] 

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными].

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>Обновление приложения для вызова настраиваемого API</h2>

1. Откройте файл index.htm в текстовом редакторе, найдите элемент **button** с именем `buttonRefresh` и добавьте сразу после него следующий новый элемент: 

		<button id="buttonCompleteAll">Complete All</button> 

	Это приведет к добавлению на страницу новой кнопки. 

2. В файле page.js после функции **refreshTodoItems** добавьте следующий код:

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	Этот метод обрабатывает событие **Click** для новой кнопки. Метод **invokeApi** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому API. Результат, возвращаемый пользовательским API, отображается в диалоговом окне сообщения, как и любые ошибки.

## <a name="test-app"></a>Тестирование приложения

1. Обновите браузер.

2. В приложении введите любой текст в поле **Вставить TodoItem**, затем щелкните **Сохранить**.

3. Повторяйте предыдущий шаг, пока в список не будут добавлены несколько элементов списка дел.

4. Нажмите кнопку **Выполнить все**. Отображается диалоговое окно сообщения, в котором указывается количество элементов, помеченных как завершенные, снова выполняется отфильтрованный запрос, что приводит к удалению из списка всех элементов.

## Дальнейшие действия

Теперь, после создания настраиваемого API и вызова его из приложения HTML, изучите следующие статьи о мобильных службах:

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-html-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-html-get-started-data
