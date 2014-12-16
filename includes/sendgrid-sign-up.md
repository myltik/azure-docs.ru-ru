Клиент Azure может разблокировать до 25 000 бесплатных сообщений электронной почты в месяц. Эти 25 000 сообщений предоставят вам доступ к расширенным отчетам и функциям аналитики, а также всем API-интерфейсам (Web, SMTP, Event, Parse, Sub-User). Сведения о дополнительных службах, предоставляемых SendGrid, см. на странице [Возможности SendGrid][].

### Регистрация для получения учетной записи SendGrid

1. Войдите на [портал управления Azure][].

2. В нижней части портала управления щелкните **Создать**.

	![command-bar-new][command-bar-new]

3. Щелкните **Магазин**.

	![sendgrid-store][sendgrid-store]

4. В диалоговом окне **Выберите надстройку** выберите **SendGrid** и щелкните стрелку вправо.

5. В диалоговом окне **Настроить надстройку** выберите план **SendGrid**, в котором вы собираетесь зарегистрироваться.

6. Введите имя для идентификации службы **SendGrid** в параметрах настройки Azure или используйте значение по умолчанию **SendGrid**. Имена должны иметь длину в диапазоне от 1 до 100 знаков и содержать только алфавитно-цифровые символы, дефисы, точки и подчеркивания. Имя должно быть уникальным в списке элементов Магазина Azure, на которые вы подписались.

	![store-screen-2][store-screen-2]

7. Выберите значение для региона, например "Запад США".

8. Щелкните стрелку вправо.

9. На вкладке **Просмотреть приобретение** просмотрите сведения о плане и ценах, а также ознакомьтесь с условиями. Если вы согласны с данными условиями, щелкните флажок. После установки флажка для учетной записи SendGrid начнется [процесс подготовки SendGrid].

	![store-screen-3][store-screen-3]

10. После подтверждения покупки вы будете перенаправлены на панель мониторинга надстроек. Появится сообщение **Приобретение SendGrid**.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

Учетная запись SendGrid создается немедленно. Появится сообщение **Надстройка SendGrid успешно приобретена**. Теперь создаются учетная запись и учетные данные. После этого вы сможете отправлять сообщения электронной почты. 

Чтобы изменить план подписки или просмотреть параметры контактов SendGrid, выберите имя службы SendGrid и откройте панель мониторинга надстройки SendGrid. 

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	Чтобы отправить сообщение электронной почты с помощью SendGrid, необходимо указать учетные данные учетной записи (имя пользователя и пароль).

### Поиск учетных данных SendGrid ###

1.  Выберите **Сведения о подключении**.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. В диалоговом окне *Сведения о подключении* скопируйте **Пароль** и имя пользователя для дальнейшего использования в этом учебнике.

	![sendgrid-connection-info][sendgrid-connection-info]

Чтобы задать параметры доставки электронной почты, нажмите кнопку **Управление**. После этого откроется веб-интерфейс Sendgrid.com, где вы сможете войти в систему и открыть панель управления SendGrid. 

	![sendgrid-control-panel][sendgrid-control-panel]

	Дополнительные сведения о начале работы с SendGrid см. в статье [Начало работы с SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Возможности SendGrid]: http://sendgrid.com/features
[Портал управления Azure]: https://manage.windowsazure.com
[Приступая к работе с SendGrid]: http://sendgrid.com/docs
[Процесс подготовки SendGrid]: https://support.sendgrid.com/hc/ru-ru/articles/200181628-Why-is-my-account-being-provisioned-


