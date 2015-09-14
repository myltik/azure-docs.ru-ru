
1. Выберите Портал Azure > **Мобильные службы** > ваша мобильная служба > **Панель мониторинга** и запишите значение поля **URL-адрес мобильной службы**.

2. Зарегистрируйте приложение у одного из следующих поставщиков проверки подлинности:
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Майкрософт](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). 
   
   Запишите значения удостоверение клиента и секрета клиента, созданные поставщиком. Не сообщайте никому этот секрет клиента.

3. На портале Azure щелкните элемент **Мобильные службы**, выберите свою мобильную службу, щелкните элемент **Удостоверение** и укажите параметры своего поставщика удостоверений, а затем введите идентификатор клиента и секрет, полученные от поставщика. 
 
Теперь мобильная служба и приложение настроены для работы с вашим поставщиком проверки подлинности. При необходимости эти действия можно повторить для каждого дополнительного поставщика удостоверений, которого нужно поддерживать.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->