<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Добавление разбиения по страницам в данные" pageTitle="Добавление разбиения по страницам в данные (Android) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как использовать разбиение по страницам для управления объемом данных, возвращаемых в ваше приложение Android из мобильных служб." metaCanonical="" services="" documentationCenter="Mobile" title="Уточнение запросов мобильных служб посредством разбиения по страницам" authors="" solutions="" manager="" editor="" />



# Уточнение запросов мобильных служб посредством разбиения по страницам
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Azure вашему приложению Android. В этом учебнике методы запроса **top** и **skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

<div class="dev-callout"><b>Примечание.</b>
<p>Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию — 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.</p>
</div>

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными]. 

1. В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами].

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

5. Повторно выполните построение и запустите приложение. 
   
    Обратите внимание, что отображаются только первые три результата из таблицы TodoItem. 

6. (Необязательно) Просмотрите URI запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, например средств разработчика браузера или [Fiddler]. 

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

   	Этот запрос пропускает первые три результата и возвращает следующие три. Это фактически вторая «страница» данных, где размер страницы составляет три элемента.

    <div class="dev-callout"><b>Примечание.</b>
    <p>В этом учебнике используется упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы <strong>top</strong> и <strong>skip</strong>. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. Можно также вызвать метод <strong>includeInlineCount</strong>, чтобы, помимо разбитых на страницы данных, получить общее количество элементов, доступных на сервере.</p>
    </div>

8. (Необязательно) Снова просмотрите URI запроса, отправленного в мобильную службу. 

   	Обратите внимание, что метод `skip(3)` был преобразован в параметр запроса `$skip=3` в URI запроса.

## <a name="next-steps"> </a>Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.
 
* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->

[Дальнейшие действия]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android

[Портал управления]: https://manage.windowsazure.com/


