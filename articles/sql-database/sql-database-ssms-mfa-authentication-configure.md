i---title: Настройка Многофакторной идентификации для SQL Azure | Документация Майкрософт description: Использование Многофакторной идентификации с SSMS для базы данных SQL и хранилища данных SQL.
services: sql-database documentationcenter: '' author: BYHAM manager: jhubbard editor: '' tags: ''

ms.assetid: ms.service: sql-database ms.custom: authentication and authorization ms.devlang: na ms.topic: article ms.tgt_pltfrm: na ms.workload: data-management ms.date: 01/23/2017 ms.author: rickbyh

---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio

В этом разделе показано, как настроить Многофакторную идентификацию Базы данных SQL Azure для SQL Server Management Studio. 

Обзор Многофакторной идентификации Базы данных SQL Azure см. в разделе [Поддержка SSMS в Azure AD MFA для базы данных SQL и хранилища данных SQL](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Этапы настройки

1. **Настройка Azure Active Directory.** Дополнительные сведения см. в статьях [Интеграция локальных удостоверений с Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Управление каталогом Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) и [AzureADHelp](https://msdn.microsoft.com/library/azure/jj151815.aspx) и записях блога [Add your own domain name to Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Добавление собственного имени домена в Azure AD) и [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory).
2. **Настройка MFA.** Пошаговые инструкции см. в статье [Настройка Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 
3. **Настройка базы данных SQL или хранилища данных SQL для аутентификации Azure AD.** Пошаговые инструкции см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).
4. **Скачивание SSMS.** Скачайте последнюю SSMS (по крайней мере за август 2016 г.) на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Подключение с помощью универсальной аутентификации и SSMS

Приведенные ниже инструкции описывают, как подключиться к базе данных SQL или хранилищу данных SQL с помощью последней версии SSMS.

1. Чтобы подключиться с использованием универсальной проверки подлинности, в диалоговом окне **Соединение с сервером** выберите **Универсальная проверка подлинности Active Directory**.

   ![1mfa-universal-connect][1]
2. Как и для базы данных SQL и хранилища данных SQL, необходимо щелкнуть **Параметры** и указать базу данных в диалоговом окне **Параметры**. Щелкните **Подключить**.
3. Когда откроется диалоговое окно **Вход в учетную запись** , укажите учетную запись и пароль своего удостоверения Azure Active Directory.

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > Если для этой учетной записи не требуется MFA, то на этом этапе универсальной аутентификации устанавливается подключение. Пользователи, которым требуется MFA, следует выполнить приведенные ниже действия.
   > 
   > 
4. Могут отобразиться два диалоговых окна настройки MFA. Эта одноразовая операция зависит от параметра администратора MFA, то есть может быть необязательной. Для домена с поддержкой MFA этот шаг иногда бывает предварительно определен (например, домен требует от пользователя использовать смарт-карту и ПИН-код).  

   ![3mfa-setup][3]
5. В диалоговом окне второй одноразовой операции можно выбрать параметры метода аутентификации. Возможные параметры настраиваются администратором.

   ![4mfa-verify-1][4]
6. Azure Active Directory отправляет вам информациею о подтверждении. Получив код проверки, введите его в поле **Введите проверочный код** и нажмите кнопку **Вход**.

   ![5mfa-verify-2][5]

Обычно по завершении проверки выполняется подключение SSMS при условии, что указаны действительные учетные данные и в брандмауэре разрешен доступ.

## <a name="next-steps"></a>Дальнейшие действия

* Обзор Многофакторной идентификации Базы данных SQL Azure см. в разделе [Поддержка SSMS в Azure AD MFA для базы данных SQL и хранилища данных SQL](sql-database-ssms-mfa-authentication.md).
* Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).  
Предоставьте другим пользователям возможность подключаться через брандмауэр: [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

