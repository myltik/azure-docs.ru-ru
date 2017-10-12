---
title: "Использование .NET Core для создания запросов к базе данных SQL Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как использовать .NET Core для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: 12837cf3b080c63d494993b53efdc209ea93f389
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-net-core-c-to-query-an-azure-sql-database"></a>Использование .NET Core (C#) для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [.NET Core](https://www.microsoft.com/net/) в Windows, Linux и Mac OS для создания программы C#, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос к данным.

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

- База данных SQL Azure. В этом кратком руководстве используются ресурсы, созданные в одном из этих кратких руководств: 

   - [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
   - [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
   - [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

- [Правило брандмауэра на уровне сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.
- Убедитесь, что установлен [.NET Core для вашей операционной системы](https://www.microsoft.com/net/core). 

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на следующем рисунке. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер базы данных SQL Azure, перейдите на соответствующую страницу, чтобы просмотреть имя администратора сервера. При необходимости вы можете сбросить пароль.

5. Щелкните **Показать строки подключения к базам данных**.

6. Просмотрите полную строку подключения **ADO.NET**.

    ![Строка подключения по протоколу ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Необходимо настроить правила брандмауэра для общедоступного IP-адреса компьютера, на котором выполняются действия из этого руководства. Если вы используете другой компьютер или имеете другой общедоступный IP-адрес, создайте [правила брандмауэра на уровне сервера с помощью портала Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-net-project"></a>Создание проекта .NET

1. Откройте командную строку и создайте папку с именем *sqltest*. Перейдите к созданной папке и выполните следующую команду:

    ```
    dotnet new console
    ```

2. Откройте ***sqltest.csproj*** с помощью предпочитаемого текстового редактора и добавьте System.Data.SqlClient в качестве зависимости, используя следующий код:

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.4.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Вставка кода для отправки запроса к базе данных SQL

1. В среде разработки или в предпочитаемом текстовом редакторе откройте **Program.cs**.

2. Замените содержимое следующим кодом и добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadLine();
        }
    }
}
```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующие команды:

   ```csharp
   dotnet restore
   dotnet run
   ```

2. Убедитесь, что возвращены первые 20 строк, а затем закройте окно приложения.


## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы с .NET Core в Windows, Linux и Mac OS с помощью командной строки](/dotnet/core/tutorials/using-with-xplat-cli).
- Узнайте, как [подключиться и отправить запрос к базе данных SQL Azure с помощью .NET Framework и Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Узнайте, как спроектировать первую базу данных SQL с помощью [SSMS](sql-database-design-first-database.md) или [.NET](sql-database-design-first-database-csharp.md).
- Дополнительные сведения о .NET см. в [этой документации](https://docs.microsoft.com/dotnet/).
