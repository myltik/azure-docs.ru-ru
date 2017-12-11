### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>Установка PowerShell
Установите последнюю версию PowerShell, если ее нет на компьютере. 

1. В веб-браузере перейдите на страницу [загрузок Azure](https://azure.microsoft.com/downloads/). 
2. Нажмите кнопку **Установка Windows** в разделе **Программы командной строки** -> **PowerShell**. 
3. Чтобы установить PowerShell, запустите **MSI**-файл. 

Подробные инструкции см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Вход в PowerShell

1. Запустите **PowerShell** на компьютере. Не закрывайте PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    ![Запуск PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Выполните следующую команду и введите те же имя пользователя Azure и пароль, которые используются для входа на портал Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Чтобы просмотреть все подписки для этой учетной записи, выполните следующую команду:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Если с вашей учетной записью связаны несколько подписок, выполните следующую команду, чтобы выбрать нужную подписку. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
