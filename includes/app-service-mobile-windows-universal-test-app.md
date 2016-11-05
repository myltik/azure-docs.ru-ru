
Вернитесь в Visual Studio и выберите проект клиентского приложения с общим кодом (его название похоже на `<your app name>.Shared`).

1. Разверните файл **App.xaml** и откройте файл **App.xaml.cs**. Найдите объявление члена `MobileService`, запускаемого с URL-адресом localhost. Закомментируйте это объявление (с помощью клавиш `CTRL+K,CTRL+C`) и раскомментируйте объявление (`CTRL+K,CTRL+U`), которое подключается к размещенной службе:
   
        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);
   
        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );
2. Нажмите клавишу F5, чтобы еще раз собрать проект, после чего запустите приложение Магазина Windows. Оно должны быть вашим проектом, запускаемым по умолчанию.
3. В приложении введите содержательный текст, например *Работа с учебником*, в поле **Вставить в TodoItem**, затем щелкните **Сохранить**.
   
    ![](./media/app-service-mobile-windows-universal-test-app/mobile-quickstart-startup.png)
   
    При этом на новый внутренний сервер мобильного приложения, размещенный в Azure, отправляется запрос POST.
4. Остановите отладку, замените запускаемый по умолчанию проект в универсальном решении Windows на приложение Магазина Windows Phone (щелкните правой кнопкой мыши проект `<your app name>.WindowsPhone` и выберите пункт **Назначить запускаемым проектом**) и снова нажмите клавишу F5.
   
    ![](./media/app-service-mobile-windows-universal-test-app/mobile-quickstart-completed-wp8.png)
   
    Обратите внимание, что данные, сохраненные на предыдущем этапе, загружаются из мобильного приложения после запуска приложения Windows.

<!---HONumber=Nov15_HO1-->