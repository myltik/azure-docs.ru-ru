	<properties urlDisplayName="Add paging to data" pageTitle="Добавление разбиения по страницам в данные (Android) | Центр разработчиков мобильных устройств" metaKeywords="" description="Узнайте, как использовать разбиение по страницам для управления объемом данных, возвращаемых в приложение Android из мобильных служб." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="ricksal" />

# Уточнение запросов мобильных служб c разбиением по страницам

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению Android. В этом учебнике методы запроса **top** и **skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию - 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с рядами данных, [Приступая к работе с данными]. 

1. В Eclipse откройте проект, созданный при прохождении учебника [Начало работы с данными].

2. В меню **Запуск** нажмите кнопку **Запуск** для запуска приложения, затем введите текст в текстовое поле и нажмите кнопку **Добавить**.

3. Повторите предыдущий шаг минимум три раза, чтобы получить более трех элементов, хранящихся в таблице TodoItem. 

4. В файле ToDoActivity.java замените метод **RefreshTodoItems** следующим кодом:

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).top(3)
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

  	Этот запрос возвращает три верхних элемента, которые не помечены как завершенные.

5. Повторно выполните сборку и запустите приложение. 
   
    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem. 

6. (Необязательно.) Просмотрите URI запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, например средств разработчика браузера или [Fiddler]. 

   	Обратите внимание, что метод `top(3)` был преобразован в параметр запроса `$top=3` в URI запроса.

7. Обновите метод **RefreshTodoItems** еще раз следующим кодом:
            
		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).skip(3).top(3)
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

   	Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы равен трем элементам.

    <div class="dev-callout"><b>Примечание.</b>
    <p>В этом учебнике используется упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы <strong>top</strong> и <strong>skip</strong>. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Можно также вызвать метод   <strong>includeInlineCount</strong>, чтобы, помимо разбитых на страницы данных, получить общее количество элементов, доступных на сервере.</p>
    </div>

8. (Необязательно.) Снова просмотрите универсальный код ресурса (URI) запроса, отправленного в мобильную службу. 

   	Обратите внимание, что метод `skip(3)` был преобразован в параметр запроса `$skip=3` в URI запроса.

## <a name="next-steps"> </a>Дальнейшие действия

На этом завершается серия учебников, демонстрирующих основы работы с данными в мобильных службах. Рассмотрите возможность лучше ознакомиться со следующими темами, касающимися мобильных служб:

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью учетной записи Windows.
 
* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

<!-- Anchors. -->

[Следующие шаги]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android

[Портал управления]: https://manage.windowsazure.com/

