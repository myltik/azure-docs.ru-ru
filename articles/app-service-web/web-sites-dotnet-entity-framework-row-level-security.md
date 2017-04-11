---
title: "Руководство по разработке веб-приложения с мультитенантной базой данных с помощью Entity Framework и политики безопасности на уровне строк"
description: "Узнайте, как разработать веб-приложение ASP.NET MVC 5 с серверной частью в виде мультитенантной базы данных SQL с помощью Entity Framework и политики безопасности на уровне строк."
metakeywords: azure asp.net mvc entity framework multi tenant row level security rls sql database
services: app-service\web
documentationcenter: .net
manager: jeffreyg
author: tmullaney
ms.assetid: 8fdc47a5-6fc3-4d29-ab6a-33e79f50699f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/25/2016
ms.author: thmullan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ba1bb3d84b462dfebbb2564569517d7336bf54fd
ms.lasthandoff: 04/10/2017


---
# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Руководство по разработке веб-приложения с мультитенантной базой данных с помощью Entity Framework и политики безопасности на уровне строк
В этом руководстве показано, как создать мультитенантное веб-приложение с [общей базой данных и общей схемой](https://msdn.microsoft.com/library/aa479086.aspx) с помощью Entity Framework и политики [безопасности на уровне строк](https://msdn.microsoft.com/library/dn765131.aspx). Подобная модель предусматривает, что в отдельной базе данных содержатся данные для многих клиентов, а каждая строка в каждой таблице связана с идентификатором клиента. Безопасность на уровне строк (RLS) — это новая функция базы данных SQL Azure, которая не позволяет клиентам обращаться к данным других клиентов. Для работы этой политики в приложение нужно внести одно небольшое изменение. Централизуя логику доступа к клиенту в самой базе данных, политика RLS упрощает код приложения и снижает риск случайной утечки данных клиентов.

Начнем с простого приложения диспетчера контактов (прочтите статью [Создание и развертывание в службе приложений Azure приложения ASP.NET MVP с проверкой подлинности и базой данных SQL Server](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)). В этом приложении все пользователи (клиенты) могут видеть все контакты.

![Приложение диспетчера контактов перед включением RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Немного изменив его, мы включим поддержку мультитенантного режима, благодаря чему пользователи будут видеть только свои контакты.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Шаг 1. Добавление в приложение класса-перехватчика для определения SESSION_CONTEXT
Сейчас нам нужно внести в приложение одно изменение. Так как все пользователи приложения подключаются к базе данных с помощью одной строки подключения (т. е. одних учетных данных SQL), текущая политика RLS не предусматривает фильтрацию пользователей. Такой подход очень часто используется в веб-приложениях, позволяя эффективно регулировать количество запросов при подключении. Но в данном случае это означает, что нам нужно настроить другой способ идентификации текущего пользователя приложения в базе данных. Решение: присвоить пару "ключ-значение" текущему идентификатору UserId в [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) сразу после открытия приложения и до того, как приложение выполнит запрос. SESSION_CONTEXT — это хранилище пар «ключ — значение» для области сеанса. Хранимый в нем идентификатор UserId будет использоваться политикой RLS для определения текущего пользователя.

Кроме того, мы добавим [перехватчик](https://msdn.microsoft.com/data/dn469464.aspx) (в частности, [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), новую функцию Entity Framework (EF) 6, используемую для автоматической настройки текущего идентификатора UserId в SESSION_CONTEXT путем добавления инструкции T-SQL независимо от того, когда EF откроет подключение.

1. Откройте проект ContactManager в Visual Studio.
2. В обозревателе решений щелкните правой кнопкой мыши папку «Модели», а затем выберите «Добавить» > «Класс».
3. Присвойте новому классу имя SessionContextInterceptor.cs и нажмите кнопку «Добавить».
4. Замените содержимое файла SessionContextInterceptor.cs кодом, приведенным ниже.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }

        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Это все, что необходимо изменить в приложении. Теперь создайте и опубликуйте приложение.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Шаг 2. Добавление столбца UserId в схему базы данных
Далее нам нужно добавить в таблицу контактов столбец UserId, чтобы связать каждую строку с пользователем (клиентом). Так как мы изменим схему непосредственно в базе данных, нам не нужно включать это поле в модель данных EF.

Подключитесь к базе данных напрямую, используя SQL Server Management Studio или Visual Studio, и выполните следующую инструкцию T-SQL.

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Столбец UserId будет добавлен в таблицу контактов. Мы выполним сопоставление идентификаторов UserId, хранимых в таблице AspNetUsers, с помощью типа данных nvarchar(128). Затем мы создадим ограничение DEFAULT, которое автоматически присваивает новым вставленным строкам значение UserId, хранимое в SESSION_CONTEXT.

Теперь таблица выглядит следующим образом.

![Таблицы контактов SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Правильное значение UserId присваивается созданным контактам автоматически. В качестве примера мы назначим существующим пользователям несколько существующих контактов.

Пользователи, созданные в приложении (например, с помощью локальной учетной записи, учетной записи Google или Facebook), отобразятся таблице AspNetUsers. На приведенном ниже снимке экрана отображен только один пользователь.

![Таблица AspNetUsers SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Скопируйте значение идентификатора для пользователя user1@contoso.com и вставьте его в приведенную ниже инструкцию T-SQL. Выполните эту инструкцию, чтобы связать три контакта с этим идентификатором UserId.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Шаг 3. Создание в базе данных политики безопасности на уровне строк
Последним шагом является создание политики безопасности, которая использует UserId в SESSION_CONTEXT для автоматической фильтрации результатов, возвращаемых запросами.

Не прерывая подключение к базе данных, выполните следующую инструкцию T-SQL.

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Этот код выполняет три действия. Во-первых, он создает новую схему для централизации и ограничения доступа к объектам RLS. Во-вторых, он создает предикатную функцию, которая возвращает 1, если значение UserId строки соответствует значению UserId в хранилище SESSION_CONTEXT. Наконец, он создает политику безопасности, которая добавляет эту функцию в таблицу контактов в качестве предиката фильтрации и блокировки. Предикат фильтрации вызывает запросы для возврата только тех строк, которые принадлежат текущему пользователю, а предикат блокировки не позволяет приложению случайно вставить строку для неправильного пользователя.

Запустите приложение и войдите как user1@contoso.com. Этот пользователь теперь видит только те контакты, которые мы перед этим назначили его идентификатору UserId:

![Приложение диспетчера контактов перед включением RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Для дальнейшей проверки зарегистрируйте нового пользователя. Новый пользователь не увидит никаких контактов, так как мы ему ничего не назначили. Чтобы увидеть контакт, новый пользователь должен создать его, после чего этот контакт будет назначен его идентификатору UserId.

## <a name="next-steps"></a>Дальнейшие действия
Вот и все! Мы преобразовали простое приложение диспетчера контактов в мультитенантное приложение, где у каждого пользователя есть собственный список контактов. Применение политики безопасности на уровне строк позволило нам избежать сложностей при реализации логики доступа к клиенту в коде приложения. Эта прозрачность позволяет использовать приложение для реальных бизнес-задач. Кроме того, это позволяет минимизировать риск случайной утечки данных, связанный с увеличением объема кода приложения.

В этом руководстве приводится только беглый обзор возможностей применения политики RLS. Например, вы можете использовать более сложную или адресную логику доступа, а в хранилище SESSION_CONTEXT могут храниться не только текущие значения идентификаторов UserId. Кроме того, в можете [интегрировать RLS в клиентские библиотеки средств работы с эластичными базами данных](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) для поддержки мультитенантных сегментов на уровне данных горизонтального масштабирования.

Мы работаем над тем, чтобы сделать политику RLS еще лучше. Если у вас возникли вопросы или идеи, которые вы хотели бы реализовать, сообщите нам об этом в комментариях. Ваши отзывы важны для нас!


