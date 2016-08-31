В этом примере я покажу, как использовать триггер **SharePoint Online - When a new item is created** (SharePoint Online — при создании нового элемента) для запуска рабочего процесса приложения логики при создании нового элемента в списке SharePoint Online.

>[AZURE.NOTE]Вам будет предложено войти с учетной записью SharePoint, если вы еще не создали *подключение* к SharePoint Online.  

1. В конструкторе приложений логики в поле поиска введите *sharepoint*, а затем выберите триггер **SharePoint Online - When a new item is created** (SharePoint Online — при создании нового элемента).  
![Триггер SharePoint Online, изображение 1](./media/connectors-create-api-sharepointonline/trigger-1.png)  
- Отобразится элемент управления **When a new item is created** (SharePoint Online — при создании нового элемента).  
![Триггер SharePoint Online, изображение 2](./media/connectors-create-api-sharepointonline/trigger-2.png)  
- Выберите **URL-адрес сайта**. Это сайт SharePoint Online, на котором необходимо отслеживать создание новых элементов для активации рабочего процесса.  
![Триггер SharePoint Online, изображение 3](./media/connectors-create-api-sharepointonline/trigger-3.png)  
- Выберите **Имя списка**. Это список на сайте SharePoint Online, в котором необходимо отслеживать создание новых элементов, чтобы активировать рабочий процесс.  
![Триггер SharePoint Online, изображение 4](./media/connectors-create-api-sharepointonline/trigger-4.png)  

На этом этапе для приложения логики настроен триггер, который будет запускать другие триггеры и действия в рабочем процессе. Это будет выполняться каждый раз при создании нового элемента в выбранном списке SharePoint Online.  

<!---HONumber=AcomDC_0727_2016-->
