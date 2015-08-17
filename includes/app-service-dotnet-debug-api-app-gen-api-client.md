## Создание клиента приложения API 

Средства приложения API в Visual Studio упрощают написание кода на C#, который вызывает приложения API Azure с рабочего стола, из хранилища и мобильных приложений.

1. В Visual Studio откройте решение, которое содержит приложение API из раздела [Создание приложения API](../article/app-service-api/app-service-dotnet-create-api-app.md). 

2. В **обозревателе решений** щелкните решение правой кнопкой мыши и последовательно выберите пункты **Добавить** > **Новый проект**.

	![Добавление нового проекта](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. В диалоговом окне **Добавление нового проекта** выполните следующие действия.

	1. Выберите категорию **Классические приложения Windows**.
	
	2. Выберите шаблон проекта **Консольное приложение**.
	
	3. Дайте проекту имя.
	
	4. Нажмите кнопку **ОК**, чтобы создать новый проект в существующем решении.
	
	![Добавление нового проекта](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. Щелкните правой кнопкой мыши только что созданный проект консольного приложения и последовательно выберите пункты **Добавить** > **Клиент приложения API Azure**.

	![Добавление нового клиента](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. В диалоговом окне **Добавление клиента приложения API Microsoft Azure** выполните следующие действия.

	1. Выберите пункт **Загрузка**. 
	
	2. В раскрывающемся списке щелкните приложение API, созданное ранее.
	
	3. Нажмите кнопку **ОК**.

	![Экран создания](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	Мастер скачает файл метаданных API и создаст типизированный интерфейс для вызова приложения API.

	![Процесс создания](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	После создания кода вы увидите новую папку в обозревателе решений с именем приложения API. В этой папке содержится код, который реализует модели клиента и данных.

	![Создание завершено](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. Откройте файл **Program.cs** из корня проекта и замените метод **Main** следующим кодом:

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## Тестирование клиента приложения API

После кодирования приложения API необходимо проверить код.

1. Откройте **обозреватель решений**.

2. Щелкните правой кнопкой мыши консольное приложение, созданное в предыдущем разделе.

3. В контекстном меню этого консольного приложения выберите **Отладка > Запустить новый экземпляр**.

4. Должно открыться окно консоли, отображающее все контакты.

	![Запуск консольного приложения](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. Нажмите клавишу **ВВОД** чтобы закрыть окно консоли.

<!---HONumber=August15_HO6-->