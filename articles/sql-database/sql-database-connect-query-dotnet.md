---
title: "Подключение к базе данных Azure SQL с помощью .NET (C#) | Документация Майкрософт"
description: "В этой статье представлен пример кода .NET, который можно использовать для подключения и выполнения запросов к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 368ffc65382c75b0fe5f4c20ce1c6a487a764ed3
ms.lasthandoff: 04/18/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью .NET (C#)

В этом кратком руководстве показано, как, используя [C# и ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx), подключиться к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных на платформах Windows, Mac OS и Ubuntu Linux.

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)

## <a name="install-net"></a>Установка .NET

### <a name="windows-net-framework-and-net-core"></a>**Windows .NET Framework и .NET Core**

Выпуск Visual Studio Community 2017 — это полнофункциональная расширяемая бесплатная среда IDE для создания современных приложений для Android, iOS, Windows, а также веб-приложений, приложений базы данных и облачных служб. Вы можете установить полную версию .NET Framework или только .NET Core. Фрагменты кода в кратком руководстве работают с любой из них. Если среда Visual Studio уже установлена на вашем компьютере, то вы можете пропустить следующие действия.

1. Скачайте [установщик](https://go.microsoft.com/fwlink/?LinkId=691978). 
2. Запустите установщик и выполните указанные действия, чтобы завершить установку.

### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать проект .NET Core. Введите следующие команды для установки **brew**, **OpenSSL** и **.NET Core**: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

Установите .NET Core на macOS. Скачайте [официальный установщик](https://go.microsoft.com/fwlink/?linkid=843444). С помощью этого установщика можно установить средства и поместить их в свой путь, чтобы запустить dotnet в окне консоли.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать проект .NET Core. Введите следующие команды для установки **.NET Core**:

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите строку подключения на портале Azure. Используйте строку подключения, чтобы подключиться к базе данных SQL Azure.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер базы данных SQL Azure, перейдите к соответствующей странице, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.

5. Щелкните **Показать строки подключения к базам данных**.

6. Просмотрите строку подключения **ADO.NET**.

    ![Строка подключения по протоколу ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)
  
## <a name="add-systemdatasqlclient"></a>Добавление System.Data.SqlClient
При использовании .NET Core добавьте System.Data.SqlClient к файлу проекта ***csproj*** в качестве зависимости.

```xml
<ItemGroup>
    <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
</ItemGroup>
```

## <a name="select-data"></a>Выбор данных

1. В среде разработки откройте файл с пустым кодом.
2. Добавьте ```using System.Data.SqlClient``` в файл кода ([пространство имен System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)). 

3. С помощью метода [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) и инструкции Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) отправьте запрос на получение данных из базы данных SQL Azure. Добавление соответствующих значений для сервера
```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
        }
    }
}
```

## <a name="insert-data"></a>Добавление данных

С помощью метода [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) и инструкции Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) вставьте данные в базу данных SQL Azure.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
                    Console.WriteLine("\nInsert data example:");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("INSERT INTO [SalesLT].[Product] ([Name], [ProductNumber], [Color], [StandardCost], [ListPrice], [SellStartDate]) ");
                    sb.Append("VALUES (@Name, @ProductNumber, @Color, @StandardCost, @ListPrice, @SellStartDate);");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ProductNumber", "200989");
                        command.Parameters.AddWithValue("@Color", "Blue");
                        command.Parameters.AddWithValue("@StandardCost", 75);
                        command.Parameters.AddWithValue("@ListPrice", 80);
                        command.Parameters.AddWithValue("@SellStartDate", "7/1/2016");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) inserted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="update-data"></a>Обновление данных

С помощью метода [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) и инструкции Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) обновите данные в базе данных SQL Azure.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
                    Console.WriteLine("\nUpdate data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("UPDATE [SalesLT].[Product] SET ListPrice = @ListPrice WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ListPrice", 500);
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) updated");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="delete-data"></a>Удаление данных

С помощью метода [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) и инструкции Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) удалите данные из базы данных SQL Azure.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
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
                    Console.WriteLine("\nDelete data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("DELETE FROM SalesLT.Product WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) deleted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Документацию по .NET см. в [документации по .NET](https://docs.microsoft.com/dotnet/).
- Дополнительные сведения о подключении к базе данных SQL с помощью SQL Server Management Studio и выполнении запроса к ней см. [здесь](sql-database-connect-query-ssms.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Visual Studio Code](sql-database-connect-query-vscode.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью PHP](sql-database-connect-query-php.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Node.js](sql-database-connect-query-nodejs.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Java](sql-database-connect-query-java.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Python](sql-database-connect-query-python.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Ruby](sql-database-connect-query-ruby.md).

