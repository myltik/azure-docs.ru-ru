## <a name="register-app-aad"></a>Регистрация клиентского приложения с помощью Azure Active Directory

1. На [портале управления Azure] перейдите в раздел **Active Directory** и щелкните свой каталог.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Щелкните вкладку **Приложения** вверху, а затем щелкните **Добавить**, чтобы добавить приложение. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Щелкните **Добавить приложение, разрабатываемое моей организацией**.

4. В мастере добавления приложений введите **Имя** для приложения и выберите тип **Собственное клиентское приложение**. Затем щелкните "Далее".

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. В поле **Универсальный код ресурса (URI) перенаправления** введите конечную точку /login/done для мобильной службы. Это значение должно быть аналогично https://todolist.azure-mobile.net/login/done..

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Перейдите на вкладку **Настройка** собственного приложения и скопируйте **Идентификатор клиента**. Этот идентификатор потребуется позднее.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Прокрутите страницу вниз до раздела **Разрешения для других приложений** и нажмите кнопку **Добавить приложение**. Выберите **Другое** в меню "Показать" и найдите элемент todo. Щелкните **TodoList**, чтобы добавить ранее зарегистрированную мобильную службу, и установите флажок готовности. Предоставьте доступ к приложению мобильной службы. Затем нажмите кнопку **Сохранить**.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

Теперь мобильная служба настроена в AAD для принятия попыток единого входа из приложения.


[портале управления Azure]: https://manage.windowsazure.com/<!--HONumber=42-->
