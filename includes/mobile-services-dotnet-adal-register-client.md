## <a name="register-app-aad"></a>Регистрация клиентского приложения с помощью Azure Active Directory

1.  На [портале управления Azure][портале управления Azure] перейдите в раздел **Active Directory** и щелкните свой каталог.

![][ ]

1.  Щелкните вкладку **Приложения** наверху, затем нажмите кнопку **ДОБАВИТЬ**, чтобы добавить приложение.

![][1]

1.  Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

2.  В мастере добавления приложения введите **имя** для приложения и выберите тип **Собственное клиентское приложение**. Затем щелкните "Далее".

![][2]

1.  В поле **Универсальный код ресурса (URI) перенаправления** введите конечную точку /login/done для мобильной службы. Это значение должно быть аналогичным https://todolist.azure-mobile.net/login/done.

![][3]

1.  Перейдите на вкладку **Конфигурация** собственного приложения и скопируйте **ИД клиента**. Этот идентификатор потребуется позднее.

![][4]

1.  Прокрутите страницу вниз до раздела **разрешений для других приложений** и предоставьте полный доступ к приложению мобильной службы, зарегистрированному ранее. Затем нажмите кнопку **Сохранить**.

![][5]

Теперь мобильная служба настроена в AAD для принятия попыток единого входа из приложения.

  [портале управления Azure]: https://manage.windowsazure.com/
  [ ]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
