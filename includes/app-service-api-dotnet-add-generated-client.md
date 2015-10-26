3. В **Обозревателе решений** щелкните правой кнопкой мыши проект (не решение) и выберите команду **Добавить > Клиент приложения API Azure**. 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. В диалоговом окне **Добавление клиента приложения API Azure ** установите переключатель **Загрузить из приложения API Azure**.

5. В раскрывающемся списке выберите приложение API, которое необходимо вызвать.

7. Нажмите кнопку **ОК**.

	![Экран создания](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	Мастер загружает файл метаданных API и создает типизированный интерфейс для вызова приложения API.

	![Процесс создания](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	После завершения создания кода в **Обозревателе решений** появится новая папка с именем приложения API. Эта папка содержит код, который реализует клиентские классы и модели данных.

	![Создание завершено](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=Oct15_HO3-->