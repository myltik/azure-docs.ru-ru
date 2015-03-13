

1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения] в центре разработчиков для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт и затем щелкните **Имя приложения**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. В Visual Studio откройте проект, созданный в ходе работы с учебником **Приступая к работе с мобильными службами**.

4. В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**. 

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	Откроется мастер **Связь приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6. Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее**и затем щелкните **Связать**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	При этом в манифест приложения будут добавлены необходимые регистрационные данные Магазина Windows.    

7. (Необязательно) Повторите шаги 4-6, чтобы зарегистрировать проект для Магазина Windows Phone из универсального приложения Windows.

8. На странице Центра разработчиков Windows для нового приложения щелкните **Службы**. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. На странице "Службы" щелкните **Сайт служб Live** в разделе **Мобильные службы Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Щелкните **Проверка подлинности службы** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета**. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] Секрет клиента и ИД безопасности пакета - это важные учетные данные для безопасного доступа. Не сообщайте никому эти секреты и не распространяйте их вместе с вашим приложением.

11. Войдя на [портал управления Azure], щелкните элемент **Мобильные службы** и выберите нужное приложение.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Щелкните вкладку **Push-уведомления**, введите значения **Секрет клиента** и **SID пакета**, полученные из WNS, затем щелкните **Сохранить**.

	>[AZURE.NOTE]При изучении этого учебника с использованием старой мобильной службы вы можете увидеть внизу вкладки **Push-уведомления** ссылку **Включение улучшенных push-уведомлений**. Щелкните по ней, чтобы обновить мобильную службу и интегрировать ее с центрами уведомлений. Это изменение нельзя будет отменить. Дополнительную информацию о включении улучшенных push-уведомлений в рабочей мобильной службе см. в <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">этом руководстве</a>. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]При задании учетных данных WNS для улучшенных push-уведомлений на вкладке **Push-уведомления** портала эти данные передаются в центры уведомлений для настройки центра уведомлений для вашего приложения.

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Портал управления Azure]: https://manage.windowsazure.com/
<!--HONumber=42-->
