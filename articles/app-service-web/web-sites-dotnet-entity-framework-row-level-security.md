<properties
	pageTitle="Руководство по разработке веб-приложения с мультитенантной базой данных с помощью Entity Framework и политики безопасности на уровне строк"
	description="Узнайте, как разработать веб-приложение ASP.NET MVC 5 с серверной частью в виде мультитенантной базы данных SQL с помощью Entity Framework и политики безопасности на уровне строк."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
	services="app-service\web"
	documentationCenter=".net"
	manager="jeffreyg"
  authors="tmullaney"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="thmullan"/>

# Руководство по разработке веб-приложения с мультитенантной базой данных с помощью Entity Framework и политики безопасности на уровне строк

В этом руководстве показано, как создать мультитенантное веб-приложение с [общей базой данных и общей схемой](https://msdn.microsoft.com/library/aa479086.aspx) с помощью Entity Framework и политики [безопасности на уровне строк](https://msdn.microsoft.com/library/dn765131.aspx). Подобная модель предусматривает, что в отдельной базе данных содержатся данные для многих клиентов, а каждая строка в каждой таблице связана с идентификатором клиента. Безопасность на уровне строк (RLS) — это новая функция базы данных SQL Azure, которая не позволяет клиентам обращаться к данным других клиентов. Для работы этой политики в приложение нужно внести одно небольшое изменение. Централизуя логику доступа к клиенту в самой базе данных, политика RLS упрощает код приложения и снижает риск случайной утечки данных клиентов.

Начнем с простого приложения диспетчера контактов (см. статью [Создание и развертывание в службе приложений Azure приложения ASP.NET MVP с проверкой подлинности и базой данных SQL Server](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)). В этом приложении все пользователи (клиенты) могут видеть все контакты.

![Приложение диспетчера контактов перед включением RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Немного изменив его, мы включим поддержку мультитенантного режима, благодаря чему пользователи будут видеть только свои контакты.

## Шаг 1. Добавление в приложение класса-перехватчика для определения SESSION\_CONTEXT

Сейчас нам нужно внести в приложение одно изменение. Так как все пользователи приложения подключаются к базе данных с помощью одной строки подключения (т. е. одних учетных данных SQL), текущая политика RLS не предусматривает фильтрацию пользователей. Такой подход очень часто используется в веб-приложениях, позволяя эффективно регулировать количество запросов при подключении. Но в данном случае это означает, что нам нужно настроить другой способ идентификации текущего пользователя приложения в базе данных. Решение: присвоить пару «ключ — значение» текущему идентификатору UserId в [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806) до того, как приложение выполнит запрос. SESSION\_CONTEXT — это хранилище пар «ключ — значение» для области сеанса. Хранимый в нем идентификатор UserId будет использоваться политикой RLS для определения текущего пользователя. *Примечание. В настоящее время SESSION\_CONTEXT — это предварительная версия функции базы данных SQL Azure.*

Кроме того, мы добавим [перехватчик](https://msdn.microsoft.com/data/dn469464.aspx), новую функцию Entity Framework (EF) 6, используемую для автоматической настройки текущего идентификатора UserId в SESSION\_CONTEXT путем добавления инструкции T-SQL до того, как EF выполнит все запросы.

1.	Откройте проект ContactManager в Visual Studio.
2.	В обозревателе решений щелкните правой кнопкой мыши папку «Модели», а затем выберите «Добавить» > «Класс».
3.	Присвойте новому классу имя SessionContextInterceptor.cs и нажмите кнопку «Добавить».
4.	Замените содержимое файла SessionContextInterceptor.cs кодом, приведенным ниже.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.SqlClient;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbCommandInterceptor
    {
        private void SetSessionContext(DbCommand command)
        {
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    // Set SESSION_CONTEXT to current UserId before executing queries
                    var sql = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId;";

                    command.CommandText = sql + command.CommandText;
                    command.Parameters.Insert(0, new SqlParameter("@UserId", userId));
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        public void NonQueryExecuting(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void NonQueryExecuted(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {

        }
        public void ReaderExecuting(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ReaderExecuted(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {

        }
        public void ScalarExecuting(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ScalarExecuted(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
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

## Шаг 2. Добавление столбца UserId в схему базы данных

Далее нам нужно добавить в таблицу контактов столбец UserId, чтобы связать каждую строку с пользователем (клиентом). Так как мы изменим схему непосредственно в базе данных, нам не нужно включать это поле в модель данных EF.

Подключитесь к базе данных напрямую, используя SQL Server Management Studio или Visual Studio, и выполните следующую инструкцию T-SQL.

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Столбец UserId будет добавлен в таблицу контактов. Мы выполним сопоставление идентификаторов UserId, хранимых в таблице AspNetUsers, с помощью типа данных nvarchar(128). Затем мы создадим ограничение DEFAULT, которое автоматически присваивает новым вставленным строкам значение UserId, хранимое в SESSION\_CONTEXT.

Теперь таблица выглядит следующим образом.

![Таблицы контактов SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Правильное значение UserId присваивается созданным контактам автоматически. В качестве примера мы назначим существующим пользователям несколько существующих контактов.

Пользователи, созданные в приложении (например, с помощью локальной учетной записи, учетной записи Google или Facebook), отобразятся таблице AspNetUsers. На приведенном ниже снимке экрана отображен только один пользователь.

![Таблица AspNetUsers SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Скопируйте значение столбца Id для пользователя user1@contoso.com и вставьте его в приведенную ниже инструкцию T-SQL. Выполните эту инструкцию, чтобы связать три контакта с этим идентификатором UserId.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## Шаг 3. Создание в базе данных политики безопасности на уровне строк

Последним шагом является создание политики безопасности, которая использует UserId в SESSION\_CONTEXT для автоматической фильтрации результатов, возвращаемых запросами.

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

Этот код выполняет три действия. Во-первых, он создает новую схему для централизации и ограничения доступа к объектам RLS. Во-вторых, он создает предикатную функцию, которая возвращает 1, если значение UserId строки соответствует значению UserId в хранилище SESSION\_CONTEXT. Наконец, он создает политику безопасности, которая добавляет эту функцию в таблицу контактов в качестве предиката фильтрации и блокировки. Предикат фильтрации вызывает запросы для возврата только тех строк, которые принадлежат текущему пользователю, а предикат блокировки не позволяет приложению случайно вставить строку для неправильного пользователя. *Примечание. В настоящее время предикат блокировки — это предварительная версия функции базы данных SQL Azure.*

Запустите приложение и войдите как user1@contoso.com. Этот пользователь теперь видит только те контакты, которые мы перед этим назначили его идентификатору UserId.

![Приложение диспетчера контактов перед включением RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Для дальнейшей проверки зарегистрируйте нового пользователя. Новый пользователь не увидит никаких контактов, так как мы ему ничего не назначили. Чтобы увидеть контакт, новый пользователь должен создать его, после чего этот контакт будет назначен его идентификатору UserId.

## Дальнейшие действия

Вот и все! Мы преобразовали простое приложение диспетчера контактов в мультитенантное приложение, где у каждого пользователя есть собственный список контактов. Применение политики безопасности на уровне строк позволило нам избежать сложностей при реализации логики доступа к клиенту в коде приложения. Эта прозрачность позволяет использовать приложение для реальных бизнес-задач. Кроме того, это позволяет минимизировать риск случайной утечки данных, связанный с увеличением объема кода приложения.

В этом руководстве приводится только беглый обзор возможностей применения политики RLS. Например, вы можете использовать более сложную или адресную логику доступа, а в хранилище SESSION\_CONTEXT могут храниться не только текущие значения идентификаторов UserId. Кроме того, в можете [интегрировать RLS в клиентские библиотеки средств работы с эластичными базами данных](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) для поддержки мультитенантных сегментов на уровне данных горизонтального масштабирования.

Мы работаем над тем, чтобы сделать политику RLS еще лучше. Если у вас возникли вопросы или идеи, которые вы хотели бы реализовать, сообщите нам об этом в комментариях. Ваши отзывы важны для нас!

<!---HONumber=Nov15_HO2-->