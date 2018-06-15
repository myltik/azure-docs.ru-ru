---
title: Многофакторная идентификация для SQL Azure | Документация Майкрософт
description: База данных SQL Azure и хранилище данных SQL Azure поддерживают подключения из SQL Server Management Studio (SSMS) с использованием универсальной проверки подлинности Active Directory.
services: sql-database
documentationcenter: ''
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: f3c94f41a4f5d7947b862054263ee07ff8ccd98c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650015"
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Универсальная проверка подлинности для Базы данных SQL и хранилища данных SQL (поддержка SSMS для MFA)
База данных SQL Azure и хранилище данных SQL Azure поддерживают подключения из SQL Server Management Studio (SSMS) с использованием *универсальной проверки подлинности Active Directory*. 
**Скачивание последней версии SSMS.** Скачайте последнюю версию SSMS на клиентский компьютер, воспользовавшись страницей [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Для всех функций в этом разделе используйте по крайней мере версию 17.2 за июль 2017 года.  Диалоговое окно подключения в последней версии выглядит следующим образом: ![1mfa универсального подключения](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Заполнение поля \"Имя пользователя\"")  

## <a name="the-five-authentication-options"></a>Пять параметров аутентификации  
- Универсальная аутентификация Active Directory поддерживает два неинтерактивных метода аутентификации (проверку пароля (`Active Directory - Password`) и встроенную аутентификацию (`Active Directory - Integrated`)). Неинтерактивные методы аутентификации (`Active Directory - Password` и `Active Directory - Integrated`) можно использовать во множестве различных приложений (ADO.NET, JDBC, ODBC и т. д.). При использовании этих двух методов никогда не отображаются всплывающие диалоговые окна.

- Проверка подлинности `Active Directory - Universal with MFA` — это интерактивный метод, который также поддерживает *Многофакторную идентификацию Azure* (MFA). Azure MFA помогает защитить доступ к данным и приложениям, при этом не усложняя процесс входа пользователей в систему. Она обеспечивает надежную аутентификацию с использованием ряда простых вариантов подтверждения: телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Пользователи сами выбирают предпочтительный способ подтверждения. Интерактивная MFA с использованием Azure AD может привести к появлению всплывающего диалогового окна для реализации проверки.

Общие сведения о Многофакторной идентификации см. в [этой статье](../active-directory/authentication/multi-factor-authentication.md).
Инструкции по настройке см. в разделе [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Параметр доменного имени или идентификатора клиента Azure AD   

Начиная с [SSMS версии 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), пользователи, импортируемые в текущий каталог Active Directory из других каталогов Azure Active Directory в качестве гостевых пользователей, могут при подключении указывать доменное имя или идентификатор клиента Azure AD. К гостевым пользователям относятся пользователи, приглашенные из других каталогов Azure AD, учетные записи Майкрософт, такие как outlook.com, hotmail.com и live.com, или другие учетные записи, например, gmail.com. Эта информация позволяет службе **универсальной аутентификации Active Directory с MFA** определить правильный центр аутентификации. Этот параметр также является обязательным для поддержки учетных записей Майкрософт (MSA), таких как outlook.com, hotmail.com или live.com, и сторонних учетных записей. Все пользователи, которые хотят выполнить аутентификацию с помощью универсальной проверки подлинности, должны ввести свое доменное имя или идентификатор клиента Azure AD. Этот параметр представляет собой текущее значение доменного имени или идентификатора клиента Azure AD, с которым связан сервер Azure. Например, если сервер Azure связан с доменом Azure AD `contosotest.onmicrosoft.com`, где пользователь `joe@contosodev.onmicrosoft.com` размещен в качестве импортированного пользователя из домена Azure AD `contosodev.onmicrosoft.com`, то доменное имя, необходимое для аутентификации этого пользователя, — `contosotest.onmicrosoft.com`. Если пользователь является собственным пользователем Azure AD, связанным с сервером Azure, а не пользователем с учетной записью MSA, то доменное имя или идентификатор клиента не требуются. Чтобы ввести параметр (начиная с SSMS версии 17.2), заполните поля в диалоговом окне **Подключение к базе данных**, выбрав аутентификацию **Active Directory — универсальная с поддержкой MFA**, нажмите кнопку **Параметры**, заполните поле **Имя пользователя**, а затем щелкните вкладку **Свойства соединения**. Установите флажок **Доменное имя AD или идентификатор клиента** и укажите центр аутентификации, введя доменное имя (**contosotest.onmicrosoft.com**) или GUID идентификатора клиента.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Поддержка Azure AD B2B   
Пользователи Azure AD, поддерживаемые в качестве гостевых пользователей в сценариях Azure AD B2B (см. раздел [Что такое служба совместной работы Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)), могут подключиться к Базе данных SQL и хранилищу данных SQL только в составе группы, созданной в текущем каталоге Azure AD и вручную сопоставленной в заданной базе данных с помощью инструкции `CREATE USER` Transact-SQL. Например, если пользователь `steve@gmail.com` приглашен в Azure AD `contosotest` (с доменом Azure AD `contosotest.onmicrosoft.com`), то в каталоге Azure AD, который содержит участника `steve@gmail.com`, нужно создать группу Azure AD, например `usergroup`. Затем эту группу для конкретной базы данных (т. е. MyDatabase) должен создать администратор SQL Azure AD или владелец базы данных Azure AD, выполнив инструкцию `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` Transact-SQL. После создания пользователя базы данных пользователь `steve@gmail.com` сможет войти в `MyDatabase` с помощью параметра аутентификации SSMS `Active Directory – Universal with MFA support`. Группа пользователей по умолчанию имеет только разрешение на подключение, и дополнительные возможности доступа к данным потребуется предоставить обычным способом. Обратите внимание на то, что пользователь `steve@gmail.com` в качестве гостевого пользователя должен установить флажок и добавить доменное имя AD `contosotest.onmicrosoft.com` в диалоговом окне **Свойства соединения** SSMS. Параметр **Доменное имя AD или идентификатор клиента** поддерживается только для параметра Universal with MFA connection (Универсальная с подключением MFA), в противном случае он неактивен.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Ограничения универсальной аутентификации для базы данных SQL и хранилища данных SQL
- SSMS и SqlPackage.exe — единственные инструменты, в настоящее время поддерживающее MFA с помощью универсальной аутентификации Active Directory.
- SSMS версии 17.2 поддерживает одновременный доступ нескольких пользователей с помощью универсальной аутентификации с MFA. В версиях 17.0 и 17.1 только одна учетная запись Azure Active Directory может войти в экземпляр SSMS с использованием универсальной аутентификации. Чтобы войти с другой учетной записью Azure AD, необходимо использовать другой экземпляр SSMS. (Это ограничение относится только к универсальной аутентификации Active Directory. Вы можете войти на разные серверы, используя проверку пароля Active Directory, встроенную аутентификацию Active Directory или аутентификацию SQL Server).
- SSMS поддерживает универсальную аутентификацию Active Directory для отображения обозревателя объектов, редактора запросов и хранилища запросов.
- SSMS версии 17.2 поддерживает мастер DacFx для экспорта, извлечения и развертывания данных базы данных. После того, как пользователь пройдет универсальную аутентификацию в диалоговом окне начальной аутентификации, мастер DacFx функционирует так же, как и для всех других методов аутентификации.
- Конструктор таблиц SSMS не поддерживает универсальную аутентификацию.
- Для универсальной аутентификации Active Directory не требуется дополнительное программное обеспечение, за исключением того, что необходимо использовать поддерживаемую версию SSMS.  
- Версия библиотеки аутентификации Active Directory (ADAL) для универсальной аутентификации была обновлена до последней выпущенной версии ADAL.dll — 3.13.9. Ознакомьтесь с [библиотекой аутентификации Active Directory версии 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Дополнительная информация

- Инструкции по настройке см. в разделе [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).  
- Предоставьте другим пользователям возможность подключаться через брандмауэр: [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).  
- [Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL или хранилища данных SQL](sql-database-aad-authentication-configure.md)  
- [Платформа приложения уровня данных Microsoft® SQL Server® (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Импорт BACPAC-файла в новую базу данных SQL Azure](../sql-database/sql-database-import.md)  
- [Экспорт базы данных SQL Azure в BACPAC-файл](../sql-database/sql-database-export.md)  
- Интерфейс C# [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- При использовании универсальной аутентификации **Active Directory с MFA** трассировка ADAL доступна начиная с версии [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Трассировка ADAL по умолчанию отключена. Включите ее в меню**Средства**, **Параметры** в разделе **Службы Azure**, **Облако Azure**, **Уровень трассировки для окна вывода ADAL** после включения **Вывод** в меню **Представление**. Эти трассировки доступны в окне вывода при выборе **параметра Azure Active Directory**.  
