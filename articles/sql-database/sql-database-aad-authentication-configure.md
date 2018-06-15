---
title: Настройка аутентификации Azure Active Directory для SQL | Документация Майкрософт
description: Сведения о подключении к Базе данных SQL, управляемому экземпляру и хранилищу данных SQL с использованием аутентификации Azure Active Directory после настройки Azure AD.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: 9a0cb3d69cd161a409d0a035be783bb255a83036
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644371"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL, управляемого экземпляра или хранилища данных SQL

В этой статье объясняется, как создать и заполнить каталог Azure AD, а затем использовать Azure AD с базой данных SQL Azure и хранилищем данных SQL. Обзор см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).

>  [!NOTE]  
>  Подключение к SQL Server на виртуальной машине Azure с использованием учетной записи Azure Active Directory не поддерживается. Вместо этого используйте учетную запись домена Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Создание и заполнение каталога Azure AD
Создайте каталог Azure AD и заполните его пользователями и группами. Azure AD может быть исходным управляемым доменом Azure AD. Azure AD может также быть локальной доменной службой Active Directory, объединенной в федерацию с Azure AD.

Дополнительные сведения см. в статьях [Интеграция локальных удостоверений с Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Добавление имени личного домена в Azure Active Directory](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory), [Управление каталогом Azure AD](../active-directory/active-directory-administer.md), [Azure Active Directory Cmdlets](/powershell/azure/overview?view=azureadps-2.0) (Командлеты для Azure Active Directory) и [Порты и протоколы, необходимые для гибридной идентификации](..//active-directory/connect/active-directory-aadconnect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Связывание подписки Azure с Azure Active Directory или добавление ее в службу

1. Свяжите свою подписку Azure с Azure Active Directory, сделав каталог доверенным для подписки Azure, в которой размещена база данных. Дополнительные сведения см. в статье о [связи между подписками Azure и Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
2. Используйте переключатель каталогов на портале Azure, чтобы перейти к подписке, связанной с предметной областью.

   **Дополнительные сведения.** Каждая подписка Azure связана отношением доверия с экземпляром Azure AD. Это означает, что она доверяет каталогу проверять подлинность пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. Данное отношение доверия, которое подписка имеет с каталогом, отличается от отношения, которую подписка имеет со всеми другими ресурсами в Azure (веб-сайтами, базами данных и т. д.), которые больше похожи на дочерние ресурсы подписки. Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Однако каталог останется в Azure, вы можете связать другую подписку с этим каталогом и продолжать управлять пользователями каталога. Дополнительные сведения о ресурсах см. в статье, посвященной [доступу к ресурсам в Azure](../active-directory/active-directory-b2b-admin-add-users.md). Дополнительные сведения об этих отношениях доверия см. в статье о [связывании подписки Azure с Azure Active Directory или добавлении ее в службу](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Создание администратора Azure AD для сервера Azure SQL Server
Каждый сервер Azure SQL Server (на котором размещена база данных SQL или хранилище данных SQL) сначала имеет одну учетную запись администратора сервера, который является администратором всего сервера Azure SQL Server. Необходимо создать второго администратора SQL Server, то есть учетную запись Azure AD. Этот участник создается как пользователь автономной базы данных в базе данных master. Администраторы с учетными записями администратора сервера являются членами роли **db_owner** в каждой пользовательской базе данных. Они входят в каждую такую базу данных как пользователи **dbo**. Дополнительные сведения о ролях администратора базы данных см. в статье [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md).

При использовании Azure Active Directory с георепликацией необходимо настроить администратора Azure Active Directory для сервера-источника и сервера-получателя. Если администратор Azure Active Directory для сервера отсутствует, то пользователи, входящие в Active Directory, будут получать сообщение о невозможности подключиться к серверу.

> [!NOTE]
> Пользователи без учетной записи Azure AD (включая учетную запись администратора Azure SQL Server) не могут создавать пользователей на основе Azure AD, так как у них нет разрешения на проверку предложенных пользователей базы данных с помощью Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Подготовка администратора Azure Active Directory для управляемого экземпляра

> [!IMPORTANT]
> Выполните следующие действия, только если вы подготавливаете управляемый экземпляр. Эта операция может выполняться только глобальным администратором или администратором компании в Azure AD. Ниже описывается процесс предоставления разрешений пользователям, имеющим разные привилегии в каталоге.

Управляемому экземпляру требуются разрешения на чтение Azure AD для успешного выполнения задач, таких как аутентификация пользователей посредством членства в группе безопасности или создание пользователей. Для этого необходимо предоставить для управляемого экземпляра разрешения на чтение Azure AD. Существуют два способа сделать это: с помощью портала и PowerShell. Ниже описываются оба способа.

1. В правом верхнем углу портала Azure щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory. 
2. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию. 

   С помощью этого шага можно связать подписку Active Directory с управляемым экземпляром, чтобы одна и та же подписка использовалась для Azure AD и управляемого экземпляра.
3. Перейдите к службе "Управляемый экземпляр Базы данных SQL" и выберите управляемый экземпляр, который вы хотите использовать для интеграции с Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4.  Щелкните баннер в верхней части страницы администрирования Active Directory. Если вы вошли в Azure AD как глобальный администратор или администратор компании, это можно сделать на портале Azure или с помощью PowerShell.

    ![Предоставление разрешений на портале](./media/sql-database-aad-authentication/grant-permissions.png)

    ![Предоставление разрешений с помощью PowerShell](./media/sql-database-aad-authentication/grant-permissions-powershell.png)
    
    Если вы вошли в Azure AD как глобальный администратор или администратор компании, это можно сделать на портале Azure или с помощью сценария PowerShell.

5. После успешного завершения операции в правом верхнем углу появится следующее уведомление.

    ![Успешное завершение](./media/sql-database-aad-authentication/success.png)

6.  Теперь вы можете выбрать администратора Azure AD для управляемого экземпляра. Для этого на странице "Администратор Active Directory" щелкните **Задать администратора**.

    ![Задание администратора](./media/sql-database-aad-authentication/set-admin.png)

7. На странице "Добавление администратора" найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**. 

   На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. Список поддерживаемых администраторов приведен в разделе [Функции и ограничения Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Управление доступом на основе ролей (RBAC) применяется только к порталу Azure и не распространяется на SQL Server.

    ![Добавление администратора](./media/sql-database-aad-authentication/add-admin.png)

8. В верхней части страницы "Администратор Active Directory" нажмите кнопку **Сохранить**.

    ![Сохранить](./media/sql-database-aad-authentication/save.png)

    Изменение администратора может занять несколько минут. После этого новый администратор появится в поле "Администратор Active Directory".

> [!IMPORTANT]
> При настройке администратора Azure AD новое имя администратора (пользователя или группы) не может уже присутствовать в виртуальной базе данных master как пользователь аутентификации SQL Server. Если оно присутствует, настройка администратора Azure AD завершится ошибкой. Будет выполнен откат его создания и появится сообщение, что такой администратор (имя) уже существует. Такой пользователь аутентификации SQL Server не входит в Azure AD, поэтому любая попытка подключиться к серверу с помощью аутентификации Azure AD завершится сбоем.

> [!TIP]
> Чтобы потом удалить учетную запись администратора, в верхней части страницы "Администратор Active Directory" щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.
 
## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Подготовка администратора Azure Active Directory для сервера базы данных SQL Azure

> [!IMPORTANT]
> Выполните следующие действия, только если вы подготавливаете сервер базы данных SQL Azure или хранилище данных Azure.

Ниже описаны две процедуры по подготовке администратора Azure Active Directory для сервера Azure SQL Server на портале Azure или с помощью PowerShell.

### <a name="azure-portal"></a>Портал Azure
1. В правом верхнем углу [портала Azure](https://portal.azure.com/) щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию. На этом шаге связанный с подпиской каталог Active Directory связывается с Azure SQL Server, чтобы одна и та же подписка использовалась для Azure AD и SQL Server. (На сервере Azure SQL Server может размещаться база данных SQL Azure или хранилище данных SQL Azure.)   
    ![choose-ad][8]   
    
2. На баннере слева выберите **Все службы**, затем в поле фильтра введите **SQL Server**. Выберите **Серверы SQL Server**. 

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)    

    >[!NOTE]
    > На этой странице, прежде чем выбрать **Серверы SQL Server**, можно щелкнуть **звезду** рядом с именем, чтобы добавить категорию в *Избранное* и добавить **серверы SQL Server** на левую панель навигации. 

1. На странице **SQL Server** выберите **Администратор Active Directory**.   
2. На странице **Администратор Active Directory** щелкните **Задать администратора**.   
    ![выбор Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. На странице **Добавление администратора** найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**. На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. (Список поддерживаемых администраторов см. в разделе о **функциях и ограничениях Azure AD** статьи [Использование аутентификации Azure Active Directory для аутентификации с помощью базы данных SQL или хранилища данных SQL](sql-database-aad-authentication.md).) Управление доступом на основе ролей (RBAC) применяется только к порталу и не распространяется на SQL Server.   
    ![выбор администратора](./media/sql-database-aad-authentication/select-admin.png)  
    
5. В верхней части страницы **Администратор Active Directory** нажмите кнопку **Сохранить**.   
    ![сохранение администратора](./media/sql-database-aad-authentication/save-admin.png)   

Изменение администратора может занять несколько минут. После этого новый администратор появится в поле **Администратор Active Directory** .

   > [!NOTE]
   > При настройке администратора Azure AD новое имя администратора (пользователя или группы) не может уже присутствовать в виртуальной базе данных master как пользователь аутентификации SQL Server. Если оно присутствует, настройка администратора Azure AD завершится ошибкой. Будет выполнен откат его создания и появится сообщение, что такой администратор (имя) уже существует. Такой пользователь аутентификации SQL Server не входит в Azure AD, поэтому любая попытка подключиться к серверу с помощью аутентификации Azure AD завершится сбоем.
   > 


Чтобы потом удалить учетную запись администратора, в верхней части страницы **Администратор Active Directory** щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="powershell"></a>PowerShell
Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

* Connect-AzureRmAccount
* Select-AzureRmSubscription

Командлеты, используемые для подготовки администратора Azure AD и управления им:

| Имя командлета | ОПИСАНИЕ |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (должен входить в текущую подписку). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |

Чтобы получить дополнительные сведения о каждой из этих команд, используйте команду PowerShell get-help, например ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Приведенный ниже скрипт выполняет подготовку группы администраторов Azure AD с именем **DBA_Group** (идентификатор объекта `40b79501-b343-44ed-9ce7-da4c8cc7353f`) для сервера **demo_server** в группе ресурсов с именем **Group-23**.

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Входной параметр **DisplayName** принимает отображаемое имя Azure AD или имя участника-пользователя. Например, ``DisplayName="John Smith"`` и ``DisplayName="johns@contoso.com"``. Для групп Azure AD поддерживается только отображаемое имя Azure AD.

> [!NOTE]
> Команда Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` не запрещает подготовку администраторов Azure AD для неподдерживаемых пользователей. Неподдерживаемого пользователя можно подготовить, но он не сможет подключиться к базе данных (См. 
> 

В следующем примере используется необязательный параметр **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Параметр **ObjectID** Azure AD является обязательным, если параметр **DisplayName** не уникален. Чтобы получить значения **ObjectID** и **DisplayName**, используйте раздел Active Directory классического портала Azure. Там можно просмотреть свойства пользователя или группы.
> 

Следующий пример возвращает сведения о текущем администраторе Azure AD для сервера Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

В примере ниже выполняется удаление администратора Azure AD.

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Кроме того, можно подготовить администратора Azure Active Directory с помощью интерфейсов REST API. Дополнительные сведения см. в справочнике по REST API управления службами и статье [Operations for Azure SQL Databases](https://msdn.microsoft.com/library/azure/dn505719.aspx) (Операции для баз данных SQL Azure).

### <a name="cli"></a>Интерфейс командной строки  
Можно также подготовить администратора Azure AD с помощью следующих команд CLI:
| Get-Help | ОПИСАНИЕ |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (должен входить в текущую подписку). |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Обновляет учетную запись администратора Active Directory для сервера SQL Server Azure или хранилища данных SQL Azure. |

Дополнительные сведения о командах CLI см. в статье [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Настройка клиентских компьютеров
Приложения или пользователи подключаются к Базе данных SQL Azure или хранилищу данных SQL, используя удостоверения Azure AD, с клиентских компьютеров. На эти компьютеры необходимо установить указанное ниже программное обеспечение.

* .NET Framework 4.6 или более поздней версии с [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Библиотека аутентификации Azure Active Directory для SQL Server (**ADALSQL.DLL**) доступна на нескольких языках (версии x86 и amd64) в центре загрузки в [библиотеке аутентификации Microsoft Active Directory для Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Вы можете выполнить эти требования, сделав следующее:

* Установка [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) или [SQL Server Data Tools для Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) соответствует требованиям .NET Framework 4.6.
* SSMS устанавливает **ADALSQL.DLL**версии x86.
* SSDT устанавливает **ADALSQL.DLL**версии amd64.
* Последняя версия Visual Studio со страницы [скачивания Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) соответствует требованиям .NET Framework 4.6, но не устанавливает необходимую версию amd64 **ADALSQL.DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD

Для проверки подлинности Azure Active Directory необходимо, чтобы пользователи базы данных создавались как пользователи автономной базы данных. Пользователь автономной базы данных на основе удостоверения Azure AD —это пользователь, у которого нет имени для входа в базу данных master и который сопоставляется с удостоверением в каталоге Azure AD, связанном с базой данных. Удостоверение Azure AD может быть учетной записью отдельного пользователя или группы. Дополнительные сведения о пользователях автономной базы данных см. в статье [Пользователи автономной базы данных — создание переносимой базы данных](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Пользователей баз данных (за исключением администраторов) невозможно создавать с помощью портала Azure. Роли RBAC не распространяются в SQL Server, Базу данных SQL или хранилище данных SQL. Роли Azure RBAC используются для управления ресурсами Azure и не относятся к разрешениям базы данных. Например, роль **Участник SQL Server** не предоставляет разрешение на подключение к Базе данных SQL или хранилищу данных SQL. Разрешение доступа должно быть предоставлено непосредственно в базе данных с помощью инструкций Transact-SQL.
>

Вы можете создать пользователя автономной базы данных на основе Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD от имени пользователя с уровнем разрешений не ниже, чем **Изменение любого пользователя**. Затем используйте следующий синтаксис Transact-SQL:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

Значением параметра *Azure_AD_principal_name* может быть имя участника-пользователя Azure AD или отображаемое имя группы Azure AD.

**Примеры.** Создание пользователя автономной базы данных, представляющего собой пользователя федеративного или управляемого домена Azure AD:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Чтобы создать пользователя автономной базы данных, представляющего собой группу Azure AD или группу федеративного домена, укажите отображаемое имя группы безопасности.
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Чтобы создать пользователя автономной базы данных, который представляет приложение и будет подключаться с помощью маркера Azure AD, выполните следующее.

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Создать пользователя напрямую из каталога Azure Active Directory можно только в том случае, если этот каталог связан с вашей подпиской Azure. Однако членов других каталогов Active Directory, являющихся импортированными пользователями в связанном каталоге Active Directory (так называемыми внешними пользователями), можно добавить в группу Active Directory в клиенте Active Directory. Создав пользователя автономной базы данных для этой группы AD, пользователи из внешнего каталога Active Directory могут получить доступ к базе данных SQL.   

Подробные сведения о создании пользователей автономной базы данных на основе удостоверений Azure Active Directory см. в статье [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Если удалить учетную запись администратора Azure Active Directory на сервере Azure SQL Server, все прошедшие аутенитификацию пользователи Azure AD не смогут подключиться к серверу. При необходимости администратор Базы данных SQL может вручную удалить неиспользуемых пользователей Azure AD.   

>  [!NOTE]
>  При получении ответа **Истек тайм-аут подключения**, возможно, потребуется задать для параметра `TransparentNetworkIPResolution` в строке подключения значение false. Дополнительные сведения см. в статье [Connection timeout issue with .NET Framework 4.6.1 — TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Проблема с временем ожидания подключения в .NET Framework 4.6.1 — TransparentNetworkIPResolution).   

   
При создании пользователь базы данных получает разрешение **Подключение** и может подключаться к этой базе данных как участник роли **Public**. Изначально пользователю доступны только разрешения, предоставленные роли **Public**, или другие разрешения, предоставленные любой группе Azure AD, в которую входит пользователь. Подготовив пользователя автономной базы данных, использующей Azure AD, можно предоставить ему дополнительные разрешения — так же, как разрешения для любого другого типа пользователя. Обычно разрешения предоставляются ролям базы данных, а затем эти роли назначаются пользователям. Подробные сведения см. в статье [Database Engine Permission Basics](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) (Основные сведения о разрешениях ядра СУБД). Дополнительные сведения о специальных ролях базы данных SQL см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).
Для учетной записи пользователя федеративного домена, импортируемая в управляемый домен, следует использовать идентификатор управляемого домена.

> [!NOTE]
> Пользователи Azure AD помечаются в метаданных базы данных как тип E (EXTERNAL_USER), а группы — как тип X (EXTERNAL_GROUPS). Дополнительные сведения см. в статье [sys.database_principals (Transact-SQL)](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Подключение к пользовательской базе данных или хранилищу данных с помощью SSMS или SSDT  
Чтобы убедиться, что администратор Azure AD настроен правильно, подключитесь к базе данных **master** с помощью учетной записи администратора Azure AD.
Вы можете подготовить пользователя автономной базы данных, использующей Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD, у которого есть доступ к базе данных.

> [!IMPORTANT]
> Проверку подлинности Azure Active Directory поддерживают [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) для Visual Studio 2015. Выпуск SSMS за август 2016 года также включает в себя поддержку универсальной аутентификации Active Directory, что позволяет администраторам требовать прохождения многофакторной проверки подлинности с помощью телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Использование удостоверения Azure AD для подключения с помощью SSMS или SSDT  

Ниже описаны действия по подключению к базе данных SQL с помощью удостоверения Azure AD с использованием SQL Server Management Studio или инструментов баз данных SQL Server.

### <a name="active-directory-integrated-authentication"></a>Встроенная аутентификация Active Directory

Этот метод следует использовать, если вы входите в систему Windows с помощью учетных данных Azure Active Directory из федеративного домена.

1. Запустите Management Studio или Data Tools и в диалоговом окне **Подключение к серверу** (или **Подключиться к ядру СУБД**) в поле **Проверка подлинности** выберите **Active Directory — встроенная**. Пароль не требуется (и его нельзя ввести), так как для подключения используются существующие учетные данные.   

    ![Выбор встроенной аутентификации Active Directory][11]
2. Нажмите кнопку **Параметры**, а затем на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения. (Параметр **Доменное имя AD или идентификатор клиента** поддерживается только для параметра **Universal with MFA connection** (Универсальная с подключением MFA), в противном случае он неактивен.)  

    ![Выбор имени базы данных][13]

## <a name="active-directory-password-authentication"></a>Проверка пароля Active Directory

Используйте этот метод при подключении с помощью имени участника Azure AD, в котором используется управляемый домен Azure AD. Его также можно использовать для федеративных учетных записей без доступа к домену, например при удаленной работе.

Используйте этот способ для проверки подлинности в базе данных или хранилище данных SQL с Azure AD для собственных федеративных пользователей Azure AD.
Собственные пользователи являются единственным исключением, созданным в Azure AD, для которых производится проверка подлинности с помощью имени и пароля, в то время как федеративный пользователь является пользователем Windows, чей домен объединен с Azure AD. Второй способ (использование пользователя и пароля) можно использовать, когда пользователю необходимо войти с учетными данными Windows, но локальный компьютер не присоединен к домену (при использовании, например, удаленного доступа). В этом случае пользователь Windows может указать учетную запись и пароль домена и пройти проверку подлинности в базе данных или хранилище данных SQL с помощью федеративных учетных данных.

1. Запустите Management Studio или Data Tools и в диалоговом окне **Подключение к серверу** (или **Подключиться к ядру СУБД**) в поле **Проверка подлинности** выберите **Active Directory — пароль**.
2. В поле **Имя пользователя** введите имя пользователя Azure Active Directory в формате **username@domain.com**. Это должна быть учетная запись из Azure Active Directory или учетная запись из федерации домена с Azure Active Directory.
3. В поле **Пароль** введите пароль пользователя для учетной записи Azure Active Directory или учетной записи федеративного домена.

    ![Выбор проверки пароля Active Directory][12]
4. Нажмите кнопку **Параметры**, а затем на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения. (См. рисунок в предыдущем варианте.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Использование удостоверения Azure AD для подключения из клиентского приложения

Ниже описаны действия по подключению к базе данных SQL с помощью удостоверения Azure AD из клиентского приложения.

###  <a name="active-directory-integrated-authentication"></a>Встроенная аутентификация Active Directory

Для использования встроенной проверки подлинности Windows необходимо включить в федерацию Active Directory вашего домена и Azure Active Directory. Клиентское приложение (или служба), подключающееся к базе данных, должно быть запущено на компьютере, присоединенном к домену, с учетными данными пользователя домена.

Чтобы подключиться к базе данных с помощью встроенной проверки подлинности и удостоверения Azure AD, ключевое слово проверки подлинности в строке подключения к базе данных должно иметь значение «Active Directory Integrated». В следующем примере кода C# используется ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Ключевое слово ``Integrated Security=True`` строки подключения не используется для подключения к базе данных SQL Azure. При создании подключения ODBC потребуется удалить пробелы и задать для параметра Authentication значение ActiveDirectoryIntegrated.

### <a name="active-directory-password-authentication"></a>Проверка пароля Active Directory

Чтобы подключиться к базе данных с помощью встроенной аутентификации и удостоверения Azure AD, ключевому слову аутентификации нужно присвоить пароль Active Directory. Строка подключения должна содержать ключевые слова для идентификатора пользователя (UID) и пароля (PWD), а также их значения. В следующем примере кода C# используется ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Дополнительные сведения о методах проверки подлинности Azure AD и примеры кода вы найдете в [демонстрации проверки подлинности Azure AD на GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Токен Azure AD
Этот метод проверки подлинности позволяет службам среднего уровня подключаться к базе данных SQL Azure или хранилищу данных SQL Azure путем получения маркера из Azure Active Directory (AAD). Он используется для реализации сложных сценариев, включая проверку подлинности на основе сертификатов. Для работы с проверкой подлинности маркера Azure AD необходимо выполнить четыре основных шага.

1. Зарегистрируйте приложение в Azure Active Directory и получите идентификатор клиента для кода. 
2. Создайте пользователя базы данных, представляющего приложение. (Выполнено ранее на шаге 6.)
3. Создайте сертификат на клиентском компьютере, на котором выполняется приложение.
4. Добавьте сертификат в качестве ключа для вашего приложения.

Пример строки подключения.

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Дополнительные сведения см. в [блоге о безопасности SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Дополнительные сведения о добавлении сертификата см. в статье [Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Ниже приведены инструкции для подключения с помощью sqlcmd версии 13.1, доступной в [Центре загрузки](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Дополнительная информация
- Общие сведения о доступе к базе данных SQL и управлении ею см. в статье [Контроль доступа к базе данных SQL Azure](sql-database-control-access.md).
- Общие сведения об именах для входа, пользователях и ролях базы данных в базе данных SQL см. в статье [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md).
- Дополнительные сведения о субъектах базы данных см. в [этой статье](https://msdn.microsoft.com/library/ms181127.aspx).
- Дополнительные сведения о ролях баз данных см. в статье [Роли уровня базы данных](https://msdn.microsoft.com/library/ms189121.aspx).
- Дополнительные сведения о правилах брандмауэра см. в статье [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

