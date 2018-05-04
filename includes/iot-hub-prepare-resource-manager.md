## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Подготовка к проверке подлинности запросов Azure Resource Manager
Необходимо выполнять проверку подлинности всех операций, выполняемых с ресурсами с помощью [Azure Resource Manager][lnk-authenticate-arm], используя Azure Active Directory. Для такой настройки проще всего использовать PowerShell или интерфейс командной строки Azure.

Прежде чем продолжать, установите [командлеты Azure PowerShell][lnk-powershell-install].

Ниже показано, как настроить проверку пароля для приложения AD с помощью PowerShell. Эти команды можно выполнять в обычном сеансе PowerShell.

1. Выполните следующую команду для входа в подписку Azure:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Если у вас есть несколько подписок Azure, то при входе в Azure вы получите доступ ко всем подпискам Azure, связанным с вашими учетными данными. Используйте следующую команду, чтобы просмотреть подписки Azure, доступные для использования:

    ```powershell
    Get-AzureRMSubscription
    ```

    Используйте следующую команду, чтобы выбрать подписку, с помощью которой будут выполняться команды для управления Центром Интернета вещей. Вы можете использовать имя подписки или идентификатор из выходных данных предыдущей команды:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Обратите внимание на **TenantId** и **SubscriptionId**. Они потребуются вам позднее.
3. Воспользуйтесь следующей командой для создания нового приложения Azure Active Directory, заменив в ней заполнители:
   
   * **{Display name}:** отображаемое имя вашего приложения, например **MySampleApp**.
   * **{Home page URL}:** URL-адрес домашней страницы вашего приложения, например **http://mysampleapp/home**. Этот URL-адрес необязательно должен указывать на реальное приложение.
   * **{Application identifier}:** уникальный идентификатор, например **http://mysampleapp**. Этот URL-адрес необязательно должен указывать на реальное приложение.
   * **{Password}:** пароль, который используется для проверки подлинности в вашем приложении.
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Запишите значение **ApplicationId** для созданного приложения. Этот идентификатор потребуется позднее.
5. Создайте новую субъект-службу с помощью следующей команды, заменив **{MyApplicationId}** на значением **ApplicationId** из предыдущего шага.
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Настройте назначение роли с помощью следующей команды, заменив **{MyApplicationId}** своим значением **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Вы завершили создание приложения Azure AD, которое позволит вам осуществлять проверку подлинности из своего пользовательского приложения C#. Позднее в рамках изучения данного руководства вам потребуются следующие значения:

* TenantId
* SubscriptionId
* ApplicationId
* Пароль

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
