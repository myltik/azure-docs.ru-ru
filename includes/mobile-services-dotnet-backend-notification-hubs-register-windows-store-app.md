1.  Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения][Отправить страницу приложения] в центре разработчиков для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт и затем щелкните **Имя приложения**.

    ![][0]

2.  Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

    ![][1]

    При этом создается новая регистрация в Магазине Windows для вашего приложения.

3.  В Visual Studio откройте проект Windows Store, созданный в ходе работы с учебником **Начало работы с мобильными службами**.

4.  В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**.

    ![][2]

    Откроется мастер **Связь приложений с Магазином Windows**.

5.  В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6.  Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее**и затем щелкните **Связать**.

    ![][3]

    Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.

7.  (Необязательно) Повторите шаги 4–6, чтобы зарегистрировать проект для магазина Windows Phone из универсального приложения Windows.

8.  На странице Центра разработчиков Windows для нового приложения щелкните **Службы**.

    ![][4]

9.  На странице "Службы" щелкните по **узлу службы Live** в разделе **Мобильные службы Azure**.

    ![][5]

10. Щелкните **Аутентификация службы** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета (SID)**.

    ![][6]

    <div class="dev-callout">

    **Примечание о безопасности**
    Секрет клиента и идентификатор безопасности (SID) пакета — это важные учетные данные для безопасного доступа. Не сообщайте никому эти секреты и не распространяйте их вместе с вашим приложением.

    </div>

11. Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][7]

12. Щелкните по вкладке **Push-уведомления**, введите значения **секрета клиента** и **SID пакета**, полученные из WNS на шаге 4, затем щелкните **Сохранить**.

    ![][8]

    > [WACOM.NOTE]При задании учетных данных WNS для расширенных push-уведомлений во вкладке **Push-уведомления** портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.



  [Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png
  [2]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [портал управления Azure]: https://manage.windowsazure.com/
  [7]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png
  [8]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png
