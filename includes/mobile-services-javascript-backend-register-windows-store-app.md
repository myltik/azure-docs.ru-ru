

1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения] в Центре разработчиков для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт и затем щелкните **Имя приложения**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. В Visual Studio 2012 Express для Windows 8 откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами].

4. В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**. 

  	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)

   	Откроется мастер **Связь приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6. Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее** и затем щелкните **Связать**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

   	Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.    

7. На странице Центра разработки для Windows для нового приложения щелкните **Службы**. 

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. На странице "Службы" щелкните **Сайт служб Live** в разделе **Мобильные службы Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

9. Щелкните **Проверка подлинности службы** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета**. 

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента и ИД безопасности пакета — это важные учетные данные для безопасного доступа. Не сообщайте никому эти секреты и не распространяйте их вместе с вашим приложением.</p>
    </div> 

10. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

11. Откройте вкладку **Push-уведомления**, щелкните элемент **Включение улучшенных push-уведомлений** и элемент **Да**, чтобы принять изменение конфигурации.


	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

	При этом обновляется конфигурация мобильной службы для использования улучшенных функциональных возможностей push-уведомлений, предоставляемых концентраторами уведомлений. Некоторые концентраторы уведомлений можно бесплатно использовать с вашей платной мобильной службой. Дополнительную информацию см. в разделе [Сведения о ценах — мобильные службы](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    <div class="dev-callout"><b>Важно!</b>
	<p>Эта операция выполняет сброс учетных данных push-уведомлений и изменяет режим работы push-методов в скриптах. Эти изменения нельзя отменить. Не используйте этот метод для добавления концентратора уведомлений в рабочую мобильную службу. Инструкции по включению улучшенных push-уведомлений в рабочей мобильной службе см. в <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">этом руководстве</a>.</p>
    </div>

12. Введите значения **Секрет клиента** и **ИД безопасности пакета**, полученные из WNS на шаге 4, затем щелкните **Сохранить**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]При задании учетных данных WNS для улучшенных push-уведомлений на вкладке **Push-уведомления** портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.

<!-- Anchors. -->



<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Портал управления Azure]: https://manage.windowsazure.com/

