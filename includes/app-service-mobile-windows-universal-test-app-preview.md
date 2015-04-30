
Вернитесь в Visual Studio и выберите проект клиентского приложения с общим кодом (его название похоже на `<имя_приложения>.Shared`)

1. Разверните файл **App.xaml** и откройте файл **App.xaml.cs**. Найдите объявление члена  `MobileService`, запускаемого с URL-адресом localhost. Закомментируйте это объявление (с помощью клавиш  `CTRL+K,CTRL+C`) и раскомментируйте объявление (`CTRL+K, CTRL+U`), которое подключается к размещенной службе:

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

2. В приложении введите содержательный текст, например *Complete the tutorial*, в текстовое поле **Добавление пункта в список дел** и нажмите кнопку **Сохранить**.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	При этом на серверную часть нового мобильного приложения, размещенного в Azure, будет отправлен запрос POST.

3. Остановите отладку, замените запускаемый по умолчанию проект в универсальном решении Windows на приложение Магазина Windows Phone (щелкните правой кнопкой мыши проект `<имя_приложения>.WindowsPhone` и выберите пункт **Назначить запускаемым проектом**) и снова нажмите клавишу F5.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Обратите внимание, что данные, сохраненные на предыдущем этапе, загружаются из мобильного приложения после запуска приложения Windows.


<!--HONumber=49-->