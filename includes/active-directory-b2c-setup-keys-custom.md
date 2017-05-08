> [!NOTE]
> Мы планируем внести улучшения и отказаться от следующих шагов.

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Создание учетных данных администратора в клиенте Azure AD B2C

В следующем разделе вам понадобится воспользоваться учетными данными, которые используют домен клиента Azure AD B2C. Чтобы сделать это, необходимо создать учетную запись администратора с такими учетными данными. Для этого выполните следующие действия:

1. На [портале Azure](https://portal.azure.com) переключитесь в контекст клиента Azure AD B2C и откройте колонку Azure AD B2C. [Покажите, как это сделать.](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. Выберите **Пользователи и группы**.
1. Выберите **Все пользователи**.
1. Щелкните **+ Новый пользователь**.
    * Задайте **имя** = `Admin`.
    * Задайте **имя пользователя** = `admin@{tenantName}.onmicrosoft.com`, где `{tenantName}` — имя вашего клиента Azure AD B2C.
1. В разделе **Роль каталога** выберите **Глобальный администратор** и нажмите кнопку **ОК**.
1. Щелкните **Создать**, чтобы создать администратора.
1. Установите флажок **Показать пароль** и скопируйте пароль.

### <a name="set-up-the-key-container"></a>Настройка контейнера ключей

Контейнер ключей используется для хранения ключей. Чтобы настроить его:

1. Откройте новую командную строку PowerShell.  Как один из способов, нажмите **клавиши Windows+R**, введите `powershell` и нажмите клавишу ВВОД.
1. Скачайте этот репозиторий, чтобы получить средство ExploreAdmin PowerShell.

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. Перейдите в папку со средством ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Импортируйте средство ExploreAdmin в PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. Убедитесь, что `b2c_1a_TokenSigningKeyContainer` еще не существует.  Замените `{tenantName}` именем вашего клиента.

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    а. Когда появится приглашение на вход, используйте учетную запись администратора, созданную в предыдущем разделе.

    b. При появлении запроса необходимо ввести номер телефона для настройки многофакторной проверки подлинности.

    c. В ответ на запрос измените свой пароль.

    d. **Должна возникнуть ошибка!**  В ошибке должно быть указано, что `b2c_1a_TokenSigningKeyContainer` не найден.  Если ошибка не возникла, так как вы уже выполнили следующие шаги, пропустите оставшуюся часть этого раздела.

1. Создайте `b2c_1a_TokenSigningKeyContainer`.  Замените `{tenantName}` именем вашего клиента.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. Создайте `b2c_1a_TokenEncryptionKeyContainer`.  Замените `{tenantName}` именем вашего клиента.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. Создайте `b2c_1a_FacebookSecret`.  Замените `{tenantName}` именем вашего клиента.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
