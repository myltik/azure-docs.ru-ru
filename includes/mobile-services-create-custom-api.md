

1. Выполните вход на [портал управления Windows Azure], нажмите **Мобильные службы**, а затем нажмите свое приложение.

	![][0]

2. Нажмите вкладку **API**, затем нажмите **Создать настраиваемый API**.

	![][1]

	Откроется диалоговое окно **Создать настраиваемый API**.

3. Наберите "_completeall_" в поле **API-имя**, а затем нажмите кнопку "Проверить".

	![][2]

	При этом создается новый интерфейс API.


	> [WACOM.NOTE]
	> Устанавливаются разрешения по умолчанию, что означает, что любой пользователь приложения может вызвать настраиваемый API. Однако код приложения не распространяется или не хранится безопасно и не может считаться безопасным. Поэтому следует ограничивать доступ к операциям, которые изменяют данные или влияют на мобильную службу, только для прошедших проверку пользователей.

4. Нажмите новую запись **completeall** в таблице API.

	![][3]

5. Нажмите вкладку **Сценарий**, замените существующий код на приведенный далее код, а затем нажмите **Сохранить**:

		exports.post = function(request, response) {
			var mssql = request.service.mssql;
			var sql = "UPDATE todoitem SET complete = 1 " + 
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
			mssql.query(sql, {
				success: function(results) {			
					if(results.length == 1)							
						response.send(200, results[0]);			
				}
			})
		};


	Этот код использует [объект mssql] для непосредственного доступа к таблице **todoitem**, чтобы задать флаг завершения для всех элементов. Поскольку используется функция **exports.post**, клиенты отправляют запрос POST для выполнения операции. Число измененных строк возвращается клиенту как целое значение.

> [WACOM.NOTE]
> <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554218.aspx" target="_blank">Запрос</a> и <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn303373.aspx" target="_blank">ответ</a>, предоставленный объектом для пользовательских функций API, реализуются <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">Express.js library</a>. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn280974.aspx" target="_blank">Пользовательские API</a>. 

Далее предстоит изменить приложение быстрого запуска, чтобы добавить новую кнопку и код, который асинхронно вызывает новый пользовательский интерфейс API.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-create-custom-api/mobile-services-selection.png
[1]: ./media/mobile-services-create-custom-api/mobile-custom-api-create.png
[2]: ./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png
[3]: ./media/mobile-services-create-custom-api/mobile-custom-api-select2.png

<!-- URLs. -->
[Портал управления Windows Azure]: https://manage.windowsazure.com/
[Объект MSSQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554212.aspx

