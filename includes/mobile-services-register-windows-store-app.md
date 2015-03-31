
1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения] в центре разработчиков для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт и затем щелкните **Имя приложения**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами].

4. В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Откроется мастер **Связь приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6. Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее** и затем щелкните **Связать**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	При этом в манифест приложения будут добавлены необходимые регистрационные данные Магазина Windows.

7. На странице Центра разработчиков Windows для нового приложения щелкните **Службы**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. На странице «Службы» щелкните Сайт **служб Live** в разделе **Мобильные службы Azure**.


	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. В **Параметрах приложения** запишите значения полей **Идентификатор клиента**, **Секрет клиента** и **Идентификатор безопасности пакета (SID)**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]Секрет клиента и ИД безопасности пакета - это важные учетные данные для безопасного доступа. Не сообщайте никому эти секреты и не распространяйте их вместе с вашим приложением.

10. (Необязательно) Щелкните **Параметры API**, включите параметр **Усиленная безопасность перенаправления**, укажите значение `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` в поле **URL-адрес перенаправления**, а затем щелкните **Сохранить**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Это включит аутентификацию учетных записей Майкрософт для вашего приложения.

11. Войдя на [портал управления Azure], щелкните элемент **Мобильные службы** и выберите нужное приложение.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Щелкните вкладку **Push-уведомления**, введите значения **Секрет клиента** и **SID пакета**, полученные из WNS на шаге 4, затем щелкните **Сохранить**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Щелкните вкладку **Удостоверение**. Обратите внимание, что значения **Секрет клиента** и **SID пакета** уже заданы на предыдущем шаге. Введите **Идентификатор клиента**, который вы ранее записали, и щелкните **Сохранить**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Теперь вы готовы использовать учетную запись Майкрософт для аутентификации в своем приложении.

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started/#create-new-service
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
