---
title: "Реализация мультитенантного приложения SaaS с помощью базы данных SQL Azure | Документация Майкрософт"
description: "Реализация мультитенантного приложения SaaS с помощью базы данных SQL Azure."
services: sql-database
documentationcenter: 
author: AyoOlubeko
manager: jhubbard
editor: monicar
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,scale out apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/08/2017
ms.author: AyoOlubek
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 0aea69d86a51c38c99a72f46737de1eea27bef83
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017


---

# <a name="implement-a-multi-tenant-saas-application-using-azure-sql-database"></a>Реализация мультитенантного приложения SaaS с помощью базы данных SQL Azure

Мультитенатное приложение — это приложение, расположенное в облачной среде и предоставляющее одинаковый набор служб сотням или тысячам клиентов, которые не используют данные совместно и не обращаются к данным друг друга. В качестве примера можно привести приложение SaaS, которое предоставляет службы для клиентов в размещенной в облаке среде. Эта модель изолирует данные для каждого клиента и улучшает распределение ресурсов для оптимизации затрат. 

В этом руководстве рассматривается создание мультитенантного приложения SaaS с помощью базы данных SQL Azure.

Из этого руководства вы узнаете следующее:
> [!div class="checklist"]
> * Как настроить среду базы данных для поддержки мультитенантного приложения SaaS с помощью шаблона базы данных на клиент.
> * Как создать каталог клиента.
> * Как подготовить клиентскую базу данных и зарегистрировать ее в каталоге клиента.
> * Как настроить пример приложения Java. 
> * Как получить доступ к клиентским базам данных через простое консольное приложение Java.
> * Как удалить клиент.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

* Последняя версия PowerShell и [последний выпуск пакета SDK для Azure PowerShell](http://azure.microsoft.com/downloads/).

* Последняя версия [SQL Server Management Studio](http://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). При установке SQL Server Management Studio также устанавливается последняя версия SqlPackage, служебной программы командной строки, которую можно использовать для автоматизации ряда задач по разработке базы данных.

* [Среда выполнения Java (JRE) версии 8](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) и [последний пакет JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). 

* [Apache Maven](https://maven.apache.org/download.cgi). Maven можно использовать для управления зависимостями, сборки, тестирования и запуска примера проекта Java.

## <a name="set-up-data-environment"></a>Настройка среды данных

Вам необходимо подготовить базу данных для каждого клиента. Модель "база данных на клиент" обеспечивает максимальный уровень изоляции между клиентами и небольшую стоимость разработки и выполнения операций. Чтобы оптимизировать стоимость облачных ресурсов, вам также необходимо подготовить клиентские базы данных для размещения в эластичном пуле, что позволит оптимизировать соотношение цены и производительности для группы баз данных. Дополнительные сведения о других моделях подготовки базы данных см. в разделе [Модели данных мультитенантного приложения](sql-database-design-patterns-multi-tenancy-saas-applications.md#multi-tenant-data-models).

Следуйте инструкциям ниже, чтобы создать SQL Server и эластичный пул для размещения всех клиентских баз данных. 

1. Создайте переменные для хранения значений, которые будут использоваться дальше. Измените переменную IP-адреса, добавив свой IP-адрес. 
   
   ```PowerShell 
   # Set an admin login and password for your database
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   
   # Create random unique names for logical server and tenants
   $servername = "server-$(Get-Random)"
   $tenant1 = "geolamice"
   $tenant2 = "ranplex"
   
   # Store current client IP address (modify to include your IP address)
   $startIpAddress = 0.0.0.0 
   $endIpAddress = 0.0.0.0
   ```
   
2. Войдите в Azure и создайте SQL Server и эластичный пул. 
   
   ```PowerShell
   # Login to Azure 
   Login-AzureRmAccount
   
   # Create resource group 
   New-AzureRmResourceGroup -Name "myResourceGroup" -Location "northcentralus"
   
   # Create logical SQL Server with firewall rules 
   New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
       -ServerName $servername `
       -Location "northcentralus" `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   
   New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
       -ServerName $servername `
       -FirewallRuleName "singleAddress" -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
   
   # Create elastic pool 
   New-AzureRmSqlElasticPool -ResourceGroupName "myResourceGroup"
       -ServerName $servername `
       -ElasticPoolName "myElasticPool" `
       -Edition "Standard" `
       -Dtu 50 `
       -DatabaseDtuMin 10 `
       -DatabaseDtuMax 20
   ```
   
## <a name="create-tenant-catalog"></a>Создание каталога клиента 

В мультитенантном приложении SaaS важно знать, где хранятся сведения для клиента. Обычно они хранятся в базе данных каталога. База данных каталога используется для хранения сопоставления между клиентом и базой данных, в которой хранятся данные клиента.  Независимо от того, как база данных используется (мультитенантная или с одним клиентом), применяется основной шаблон.

Выполните приведенные ниже инструкции, чтобы создать базу данных каталога для примера приложения SaaS.

```PowerShell
# Create empty database in pool
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "tenantCatalog" `
    -ElasticPoolName "myElasticPool"

# Create table to track mapping between tenants and their databases
$commandText = "
CREATE TABLE Tenants
(
   TenantId         INT IDENTITY PRIMARY KEY,
   TenantName       NVARCHAR(128) NOT NULL,
   TenantDatabase   NVARCHAR(128) NOT NULL
);

CREATE INDEX IX_TenantName ON Tenants (TenantName);"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant1-and-register-in-tenant-catalog"></a>Подготовка базы данных для клиента tenant1 и его регистрация в клиентском каталоге 
Используйте Powershell, чтобы подготовить базу данных для нового клиента tenant1 и зарегистрировать его в каталоге. 

```PowerShell
# Create empty database in pool for 'tenant1'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant1');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant1 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register 'tenant1' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant1', '$tenant1');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant2-and-register-in-tenant-catalog"></a>Подготовка базы данных для клиента tenant2 и его регистрация в клиентском каталоге
Используйте Powershell, чтобы подготовить базу данных для нового клиента tenant2 и зарегистрировать его в каталоге. 

```PowerShell
# Create empty database in pool for 'tenant2'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant2 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant2');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant2 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register tenant 'tenant2' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant2', '$tenant2');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="set-up-sample-java-application"></a>Настройка примера приложения Java 

1. Создайте проект Maven. В окне командной строки введите следующее:
   
   ```
   mvn archetype:generate -DgroupId=com.microsoft.sqlserver -DartifactId=mssql-jdbc -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```
   
2. Добавьте зависимость, уровень языка и параметр сборки для поддержки файлов манифестов в JAR в файл pom.xml:
   
   ```XML
   <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
   </dependency>
   
   <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   
   <build>
        <plugins>
           <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-jar-plugin</artifactId>
              <version>3.0.0</version>
              <configuration>
                 <archive>
                    <manifest>
                       <mainClass>com.sqldbsamples.App</mainClass>
                    </manifest>
                 </archive>
              </configuration>
           </plugin>
        </plugins>
   </build>
   ```

3. В файл App.java добавьте следующее:

   ```java 
   package com.sqldbsamples;
   
   import java.util.Map;
   
   import java.util.HashMap;
   
   import java.io.BufferedReader;
   
   import java.io.InputStreamReader;
   
   import java.sql.Connection;
   
   import java.sql.Statement;
   
   import java.sql.PreparedStatement;
   
   import java.sql.ResultSet;
   
   import java.sql.DriverManager;
   
   public class App {
   
   private static final String SERVER_NAME = "your-server-name";
   
   private static final String CATALOG_DB_NAME = "tenantCatalog";
   
   private static final String USER = "ServerAdmin";
   
   private static final String PASSWORD = "ChangeYourAdminPassword1";
   
   private static final String CATALOG_DB_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, CATALOG_DB_NAME, USER, PASSWORD);
   
   private static final String CMD_LIST = "LIST";

   private static final String CMD_QUERY = "QUERY";

   private static final String CMD_QUIT = "QUIT";
   
   public static void main(String[] args) {
   
   System.out.println("\n############################");
   
   System.out.println("## SAAS DATABASE TUTORIAL ##");
   
   System.out.println("############################\n");
   
   System.out.println("OPTIONS");
   
   System.out.println(" " + CMD_LIST + " - list tenants");
   
   System.out.println(" " + CMD_QUERY + " <NAME> - connect and tenant query tenant <NAME>");
   
   System.out.println(" " + CMD_QUIT + " - quit the application\n");
   
   try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
   
   while(true) {
   
   String[] input = br.readLine().split("\\s");
   
   if (null != input && input.length > 0) {
   
   if (input[0].equalsIgnoreCase(CMD_LIST)) {
   
   listTenants();
   
   } else if (input[0].toLowerCase().startsWith(CMD_QUERY.toLowerCase()) && input.length == 2) {
   
   queryTenant(input[1].trim());
   
   } else if (input[0].equalsIgnoreCase(CMD_QUIT)) {
   
   break;
   
   } else {
   
   System.out.println(" -> Command not supported");
   
   }
   
   }
   
   System.out.println("");
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void listTenants() {
   
   // List all tenants that currently exist in the system
   
   String sql = "SELECT TenantName FROM Tenants";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   Statement stmt = connection.createStatement();
   
   ResultSet resultSet = stmt.executeQuery(sql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void queryTenant(String name) {
   
   // Query the data that was previously inserted into the primary database from the geo replicated database
   
   String url = null;
   
   String sql = "SELECT TenantDatabase FROM Tenants WHERE TenantName = ?";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   PreparedStatement pstmt = connection.prepareStatement(sql)) {
   
   pstmt.setString(1, name);
   
   try (ResultSet resultSet = pstmt.executeQuery()) {
   
   if (resultSet.next()) {
   
   url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, resultSet.getString(1), USER, PASSWORD);
   
   }
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   if (null != url) {
   
   String tenantSql = "SELECT TenantName FROM WhoAmI";
   
   try (Connection connection = DriverManager.getConnection(url);
   
   Statement stmt = connection.createStatement();

   ResultSet resultSet = stmt.executeQuery(tenantSql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> Entry in table WhoAmI in tenant " + name + " is: " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   } else {
   
   System.out.println(" -> Tenant " + name + " not found");
   
   }
   
   }
   
   }
   ```

4. Сохраните файл.

5. Перейдите в командную консоль и выполните команду ниже.

   ```bash
   mvn package
   ```

6. После ее завершения выполните следующую команду, чтобы запустить приложение: 
   
   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```
   
В случае успешного запуска выходные данные будут выглядеть примерно следующим образом:

```
############################

## SAAS DATABASE TUTORIAL ##

############################

OPTIONS

LIST - list tenants

QUERY <NAME> - connect and tenant query tenant <NAME>

QUIT - quit the application

* List the tenants

* Query tenants you created
```

## <a name="delete-first-tenant"></a>Удаление первого клиента 
Используйте PowerShell, чтобы удалить клиентскую базу данных и запись каталога для первого клиента.

```PowerShell
# Remove 'tenant1' from catalog 
$commandText = "DELETE FROM Tenants WHERE TenantName = '$tenant1';"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Delete database 
Remove-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1
```

Попробуйте подключиться к клиенту tenant1 с помощью приложения Java. Вы получите ошибку с сообщением о том, что клиент не существует.

## <a name="next-steps"></a>Дальнейшие действия 

Из этого руководства вы узнали следующее:
> [!div class="checklist"]
> * Как настроить среду базы данных для поддержки мультитенантного приложения SaaS с помощью шаблона базы данных на клиент.
> * Как создать каталог клиента.
> * Как подготовить клиентскую базу данных и зарегистрировать ее в каталоге клиента.
> * Как настроить пример приложения Java. 
> * Как получить доступ к клиентским базам данных через простое консольное приложение Java.
> * Как удалить клиент.

* Примеры PowerShell для выполнения стандартных задач см. в статье [Примеры Azure PowerShell для базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-powershell-samples).

* Дополнительные сведения о шаблонах разработки для мультитенантных приложений SaaS и базы данных SQL Azure см. в [этой статье](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications).

* Примеры Java для стандартных задач Azure см. в [центре разработчиков Java](https://azure.microsoft.com/develop/java/).




