---
title: "Поддержка SSMS в Azure AD MFA для базы данных SQL и хранилища данных SQL | Документация Майкрософт"
description: "Использование Multi-Factor Authentication с SSMS для базы данных SQL и хранилища данных SQL."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/04/2016
ms.author: rick.byham@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c9a37db7c0b2306ce4a29d23e172a8d4b4d3e954


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Поддержка SSMS в Azure AD MFA для базы данных SQL и хранилища данных SQL
База данных SQL Azure и хранилище данных SQL Azure теперь поддерживают подключения из SQL Server Management Studio (SSMS) с использованием *универсальной проверки подлинности Active Directory*. Универсальная проверка подлинности Active Directory — это интерактивный рабочий процесс, который поддерживает *многофакторную идентификацию Azure* (MFA). Azure MFA помогает защитить доступ к данным и приложениям, при этом не усложняя процесс входа пользователей в систему. Она обеспечивает надежную аутентификацию с использованием ряда простых вариантов подтверждения: телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Пользователи сами выбирают предпочтительный способ подтверждения. Общие сведения о многофакторной идентификации см. в [этой статье](../multi-factor-authentication/multi-factor-authentication.md).

Теперь SSMS поддерживает следующие функции:

* Интерактивная MFA с использованием Azure AD и потенциалом для реализации проверки со всплывающими диалоговыми окнами.
* Неинтерактивные методы аутентификации (проверка пароля Active Directory и встроенная аутентификация Active Directory), которые можно использовать во множестве различных приложений (ADO.NET, JDBC, ODBC и т. д.). При использовании этих двух методов никогда не отображаются всплывающие диалоговые окна.

Если для учетной записи пользователя настроена MFA, то рабочий процесс интерактивной аутентификации требует дополнительного взаимодействия с пользователем посредством всплывающих диалоговых окон, использования смарт-карты и т. д. Если учетная запись пользователя настроена для MFA, то пользователь должен выбрать универсальную аутентификацию Azure для подключения. Если учетная запись пользователя не требует использовать MFA, то пользователь по-прежнему может использовать другие два варианта аутентификации Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Ограничения универсальной аутентификации для базы данных SQL и хранилища данных SQL
* SSMS — единственное средство, в настоящее время поддерживающее MFA с помощью универсальной аутентификации Active Directory.
* Только одна учетная запись Azure Active Directory может войти в экземпляр SSMS с использованием универсальной аутентификации. Чтобы войти с другой учетной записью Azure AD, необходимо использовать другой экземпляр SSMS. (Это ограничение относится только к универсальной аутентификации Active Directory. Вы можете войти на разные серверы, используя проверку пароля Active Directory, встроенную аутентификацию Active Directory или аутентификацию SQL Server).
* SSMS поддерживает универсальную аутентификацию Active Directory для отображения обозревателя объектов, редактора запросов и хранилища запросов.
* Ни конструктор схем, ни DacFx не поддерживают универсальную аутентификацию.
* Учетные записи MSA не поддерживаются для универсальной аутентификации Active Directory.
* Универсальная проверка подлинности Active Directory не поддерживается в SSMS для пользователей, импортируемых в текущий каталог Active Directory из других каталогов Azure Active Directory. Это связано с тем, что для проверки учетных записей требуется идентификатор клиента, а механизмы для его предоставления отсутствуют.
* Для универсальной аутентификации Active Directory не требуется дополнительное программное обеспечение, за исключением того, что необходимо использовать поддерживаемую версию SSMS.

## <a name="configuration-steps"></a>Этапы настройки
Для реализации Multi-Factor Authentication требуется выполнить четыре основных шага.

1. **Настройка Azure Active Directory.** Дополнительные сведения см. в статьях [Интеграция локальных удостоверений с Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Управление каталогом Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) и [AzureADHelp](https://msdn.microsoft.com/library/azure/jj151815.aspx) и записях блога [Add your own domain name to Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Добавление собственного имени домена в Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory).
2. **Настройка MFA.** Пошаговые инструкции см. в статье [Настройка многофакторной идентификации Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 
3. **Настройка базы данных SQL или хранилища данных SQL для проверки подлинности Azure AD.** Пошаговые инструкции см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).
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
Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).  
Предоставьте другим пользователям возможность подключаться через брандмауэр: [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png




<!--HONumber=Dec16_HO2-->


