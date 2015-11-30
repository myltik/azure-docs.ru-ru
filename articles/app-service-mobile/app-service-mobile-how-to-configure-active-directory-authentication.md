<properties 
	pageTitle="Настройка проверки подлинности Azure Active Directory для приложения служб приложений" 
	description="Узнайте, как настроить проверку подлинности Azure Active Directory для приложения служб приложений." 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="10/29/2015" 
	ms.author="mahender"/>

# Настройка приложения службы приложений для использования службы входа Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как настроить службы приложений Azure для использования Azure Active Directory в качестве поставщика проверки подлинности.


	> [AZURE.NOTE] This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. If using the gateway, please see the [alternative method]. Differences that apply to using the gateway are called out in notes throughout that section.


## <a name="express"> </a>Настройка Azure Active Directory с помощью стандартных параметров

13. На [портале управления Azure] перейдите к нужному приложению. Щелкните **Параметры**, а затем **Аутентификация или авторизация**.

14. Если компонент проверки подлинности и авторизации не включен, установите переключатель **Вкл**.

15. Щелкните **Azure Active Directory**, а затем в разделе **Режим управления** щелкните **Экспресс**.

16. Нажмите кнопку **ОК**, чтобы зарегистрировать приложение в Azure Active Directory. При этом будет создана новая регистрация. Если вы хотите выбрать имеющуюся регистрацию, щелкните **Выбрать существующее приложение** и найдите имя ранее созданной регистрации в вашем клиенте. Щелкните регистрацию, чтобы ее выбрать, и нажмите кнопку **ОК**. Затем нажмите кнопку **ОК** в колонке параметров Azure Active Directory.

    ![][0]
	
16. По умолчанию служба приложений предоставляет вход, но не ограничивает доступ к содержимому сайта и API-интерфейсов: эти функции должны осуществляться кодом приложения. Если вы хотите полностью защитить веб-сайт с помощью службы входа Azure Active Directory, выберите в раскрывающемся списке **Действие, выполняемое, если запрос не прошел проверку подлинности** пункт **Azure Active Directory**. Будет настроено требование прохождения проверки подлинности всеми запросами. Запросы, не прошедшие проверку подлинности, будут перенаправляться для входа с помощью Azure Active Directory.

17. Щелкните **Сохранить**.

Теперь вы можете использовать Azure Active Directory для проверки подлинности в приложении.

## <a name="advanced"> </a>(Альтернативный способ) Ручная настройка расширенных параметров Azure Active Directory
Можно также указать параметры конфигурации вручную. Этот способ рекомендуется, если клиент AAD, который вы хотите использовать, отличается от клиента, с которого вы выполняете вход в Azure. Чтобы выполнить настройку, сначала необходимо создать регистрацию в Azure Active Directory, а затем предоставить некоторые сведения о регистрации службе приложений.

### <a name="register"> </a>Регистрация приложения в Azure Active Directory

1. Войдите на [портал управления предварительной версии Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он будет использоваться для настройки приложения Azure Active Directory.

3. Войдите на [портал управления Azure] и перейдите в **Active Directory**.

    ![][2]

4. Выберите свой каталог, а затем откройте вкладку **Приложения** в верхней части окна. Щелкните **Добавить** в нижней части окна, чтобы зарегистрировать новое приложение.

5. Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

6. В мастере добавления приложения введите **Имя** для приложения и выберите тип **Веб-приложение и/или веб-API**. Затем щелкните "Далее".

7. В поле **URL-адрес входа** вставьте скопированный ранее URL-адрес приложения. Введите этот же URL-адрес в поле **URI кода приложения**. Затем щелкните "Далее".

8. После добавления приложения щелкните вкладку **Настройка**. Измените **URL-адрес ответа** в разделе **Единый вход** на URL-адрес вашего приложения, дополненный путем _/.auth/login/aad/callback_. Например, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Убедитесь, что используете схему HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]При использовании шлюза службы приложений вместо компонента "Аутентификация или авторизация" службы приложений в качестве URL-адрес ответа необходимо указать URL-адрес шлюза, дополненный путем _/signin-aad_.


9. Щелкните **Сохранить**. Затем скопируйте **идентификатор клиента** для приложения. Вы настроите его позднее.

10. На нижней панели команд щелкните **Просмотр конечных точек**, скопируйте URL-адрес **документа метаданных федерации** и скачайте документ или перейдите к нему в браузере.

11. В корневом элементе **EntityDescriptor** должен быть указан атрибут **entityID** в формате `https://sts.windows.net/`, за которым следует идентификатор GUID данного клиента (называемый "идентификатором клиента"). Скопируйте это значение — оно будет использоваться в качестве вашего **URL-адреса издателя**. Вы настроите его позднее.

### <a name="secrets"> </a>Добавление сведений об Azure Active Directory в приложение


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Azure Active Directory**. Paste in the ClientID and add the tenant ID to the **Allowed Tenants** list. Click **Save**.


13. Вернитесь на [портал управления предварительной версии Azure] и перейдите к своему приложению. Щелкните **Параметры**, а затем **Аутентификация или авторизация**.

14. Если компонент проверки подлинности и авторизации не включен, установите переключатель **Вкл**.

15. Щелкните **Azure Active Directory**, а затем в разделе **Режим управления** нажмите **Дополнительно**. Вставьте значения идентификатора клиента и URL-адреса издателя, полученные ранее. Нажмите кнопку **ОК**.

    ![][1]
	
16. По умолчанию служба приложений предоставляет вход, но не ограничивает доступ к содержимому сайта и API-интерфейсов: эти функции должны осуществляться кодом приложения. Если вы хотите полностью защитить веб-сайт с помощью службы входа Azure Active Directory, выберите в раскрывающемся списке **Действие, выполняемое, если запрос не прошел проверку подлинности** пункт **Azure Active Directory**. Будет настроено требование прохождения проверки подлинности всеми запросами. Запросы, не прошедшие проверку подлинности, будут перенаправляться для входа с помощью Azure Active Directory.

17. Щелкните **Сохранить**.

Теперь вы можете использовать Azure Active Directory для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[портал управления предварительной версии Azure]: https://portal.azure.com/
[портал управления Azure]: https://manage.windowsazure.com/
[портале управления Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[alternative method]: #advanced

<!---HONumber=Nov15_HO4-->