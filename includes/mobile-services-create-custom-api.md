

1. Выполните вход на портал управления Azure, щелкните пункт **Мобильные службы**, а затем выберите свою мобильную службу.

2. Откройте вкладку **API**, а затем выберите **Создать**. Откроется диалоговое окно **Создать настраиваемый API**.

3. Введите _completeall_ в поле **Имя API** и нажмите кнопку "Проверит".

	> [AZURE.NOTE]При наличии разрешений по умолчанию любой пользователь, имеющий ключ приложения, может вызвать этот настраиваемый интерфейс API. Однако ключ приложения не является безопасным, поскольку он может распространяться или храниться небезопасным образом. Для повышения уровня безопасности рекомендуется предоставлять доступ только пользователям, прошедшим проверку.

4. Щелкните элемент **completeall** в таблице API.

5. Откройте вкладку **Сценарий**, замените существующий код на приведенный ниже, а затем нажмите кнопку **Сохранить**: Этот код использует [объект mssql] для непосредственного доступа к таблице **todoitem**, чтобы задать флаг `complete` для всех элементов. Поскольку используется функция **exports.post**, клиенты отправляют запрос POST для выполнения операции. Число измененных строк возвращается клиенту как целое значение.


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



> [AZURE.NOTE]Объекты <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">request</a> и <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">response</a>, предоставленные функциям пользовательского API, реализуются <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">библиотекой Express.js</a>. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">Настраиваемый интерфейс API</a>.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[объект mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=62-->