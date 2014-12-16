

1. Если ваше приложение еще не зарегистрировано, перейдите к разделу [Отправить страницу приложения] в Центре разработчиков для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Microsoft и затем щелкните **Имя приложения**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  Введите имя приложения в поле **Имя приложения**, выберите **Зарезервировать имя приложения**, а затем щелкните **Сохранить**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. В среде Visual Studio откройте проект Магазина Windows, созданный в ходе работы с учебником **Приступая к работе с мобильными службами**.

4. В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**. 

  	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	Откроется мастер **Связи приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Microsoft.

6.  Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее**, а затем щелкните **Связать**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

При этом в манифест приложения добавляются необходимые регистрационные данные Магазина Windows.    

7. (Необязательно) Повторите шаги 4-6, чтобы зарегистрировать проект для магазина Windows Phone из универсального приложения Windows.

8. На странице Центра разработки для Windows для нового приложения щелкните **Службы**. 

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. На странице "Службы" щелкните **Сайт служб Live** в разделе **Мобильные службы Windows Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Щелкните **Проверка подлинности службы** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета**. 

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента и ИД безопасности пакета - это важные учетные данные для безопасного доступа. Не сообщайте никому эти секреты и не распространяйте их вместе с вашим приложением.</p>
    </div> 

11. Войдите в [Портал управления Azure], щелкните **Мобильные службы**, затем щелкните свое приложение.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Откройте вкладку **Push-уведомления**, введите **секрет клиента** и **SID пакета**, полученные из WNS на шаге 4, а затем нажмите **Сохранить**.	

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]При задании учетных данных WNS для расширенных push-уведомлений на вкладке **Push-уведомления** портала эти сведения передаются в концентраторы уведомлений, чтобы настроить их для вашего приложения.

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Портал управления Azure]: https://manage.windowsazure.com/
