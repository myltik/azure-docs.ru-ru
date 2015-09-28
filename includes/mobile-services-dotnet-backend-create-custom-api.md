

1. В Visual Studio щелкните правой кнопкой мыши папку "Контроллеры", разверните **Добавить**, а затем щелкните **Новый элемент формирования шаблонов**. Откроется диалоговое окно добавления элемента скаффолдинга.

2. Разверните **Мобильные службы Azure** и выберите элемент **Настраиваемый контроллер мобильных служб Azure**, нажмите кнопку **Добавить**, введите в поле **Имя** контроллера значение `CompleteAllController`, а затем еще раз нажмите кнопку **Добавить**.

	![Диалоговое окно добавления элемента скаффолдинга веб-интерфейса API](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	При этом создается новый пустой класс контроллера с именем **CompleteAllController**.

	>[AZURE.NOTE]Если диалоговое окно не содержит элементов скаффолдинга, характерных для мобильных служб, вместо этого создайте новый **пустой контроллер веб-API**. Добавьте в этот новый класс контроллера открытое свойство **Services**, которое возвращает тип **ApiServices**. Это свойство используется для доступа к параметрам, характерным для сервера, непосредственно из вашего контроллера.

3. В **CompleteAllController.cs** добавьте следующие операторы **using**: Замените `todolistService` пространством имен своего проекта мобильной службы, которое должно быть именем мобильной службы с добавлением `Service`.

		using System.Threading.Tasks;
		using todolistService.Models;

4. В **CompleteAllController.cs** добавьте следующий класс, чтобы поместить в программу-оболочку ответ, отправленный клиенту.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. Добавьте в новый контроллер следующий код: Замените `todolistContext` именем DbContext для своей модели данных, которое должно быть именем мобильной службы с добавлением `Context`. Аналогично замените имя схемы в операторе UPDATE именем своей мобильной службы. Этот код использует [класс Database](http://msdn.microsoft.com/library/system.data.entity.database.aspx) для непосредственного доступа к таблице **TodoItems**, чтобы задать флаг завершения для всех элементов. Этот метод поддерживает запрос POST, при этом число измененных строк возвращается клиенту как целое значение.


	    // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

	> [AZURE.TIP]При наличии разрешений по умолчанию любой пользователь, имеющий ключ приложения, может вызвать этот настраиваемый интерфейс API. Однако ключ приложения не является безопасным, поскольку он может распространяться или храниться небезопасным образом. Для повышения уровня безопасности рекомендуется предоставлять доступ только пользователям, прошедшим проверку.

<!---HONumber=Sept15_HO3-->