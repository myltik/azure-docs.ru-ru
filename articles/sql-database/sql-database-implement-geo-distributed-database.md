---
title: Реализация решения географически распределенной базы данных SQL Azure | Документация Майкрософт
description: Сведения о настройке базы данных SQL Azure и приложения для отработки отказа в реплицированную базу данных и тестовой отработки отказа.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,business continuity
ms.topic: tutorial
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: fbd239c3c8c11b1907a6d28eb95d2c0ad26cfe61
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31416625"
---
# <a name="implement-a-geo-distributed-database"></a>Реализация географически распределенной базы данных

В этом руководстве вы настроите базу данных SQL Azure и приложение для отработки отказа в удаленный регион, а затем протестируете план отработки отказа. Вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * создавать пользователей базы данных и предоставлять им разрешения;
> * настраивать правила брандмауэра уровня базы данных;
> * создавать [группу отработки отказа георепликации](sql-database-geo-replication-overview.md);
> * создавать и компилировать приложения Java для запроса базы данных SQL Azure;
> * выполнять отработку аварийного восстановления.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.


## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством выполните следующие предварительные требования:

- Установите последнюю версию [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs). 
- Установите базу данных SQL Azure. В этом руководстве используется пример базы данных AdventureWorksLT с именем **mySampleDatabase** из одного из этих кратких руководств:

   - [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
   - [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
   - [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

- После определения метода для выполнения сценариев SQL для базы данных можно использовать одно из следующих средств запроса:
   - Редактор запросов на [портале Azure](https://portal.azure.com). Дополнительные сведения об использовании редактора запросов на портале Azure см. в разделе [Отправка запросов к базе данных SQL](sql-database-get-started-portal.md#query-the-sql-database).
   - Последнюю версию [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), которая является интегрированной средой для управления любой инфраструктурой SQL, от SQL Server до базы данных SQL для Microsoft Windows.
   - Последнюю версию [Visual Studio Code](https://code.visualstudio.com/docs), которая является графическим редактором кода для Linux, macOS и Windows, поддерживающим различные расширения, включая [расширение mssql](https://aka.ms/mssql-marketplace), для выполнения запросов к Microsoft SQL Server, базе данных SQL Azure и хранилищу данных SQL. Дополнительные сведения об использовании этого средства в базе данных SQL Azure см. в статье [База данных SQL Azure: подключение и запрос данных с помощью Visual Studio Code](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Создание пользователей базы данных и предоставление им разрешений

Подключитесь к базе данных и создайте учетные записи пользователей с помощью одного из следующих средств запроса:

- редактора запросов на портале Azure;
- SQL Server Management Studio
- Visual Studio Code.

Эти учетные записи пользователей автоматически реплицируются на сервер-получатель (синхронизация обязательна). Чтобы использовать SQL Server Management Studio или Visual Studio Code, вам может потребоваться настроить правило брандмауэра при подключении из клиента по IP-адресу, для которого еще не настроен брандмауэр. Подробные инструкции см. в разделе [Создание правила брандмауэра на уровне сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Чтобы создать в базе данных две учетные записи пользователя, в окне запроса выполните следующий запрос: Этот скрипт предоставляет разрешения **db_owner** для учетной записи **app_admin**, а также разрешения **SELECT** и **UPDATE** для учетной записи **app_user**. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Создание брандмауэра на уровне базы данных

Создайте [правило брандмауэра уровня базы данных](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) для базы данных SQL. Оно автоматически реплицируется на сервер-получатель, созданный в рамках этого руководства. Для удобства (в рамках этого руководства) используйте общедоступный IP-адрес компьютера, на котором выполняются действия из этого руководства. Ознакомьтесь с разделом [Создание правила брандмауэра на уровне сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule), чтобы определить IP-адрес, используемый для правила брандмауэра уровня сервера для текущего компьютера.  

- В открытом окне запроса замените предыдущий запрос следующим запросом, указав соответствующие IP-адреса для вашей среды.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Создание группы автоматической отработки отказа и активная георепликация 

С помощью Azure PowerShell создайте [группу автоматической отработки отказа активной георепликации](sql-database-geo-replication-overview.md) между существующим сервером SQL Azure и новым пустым сервером SQL Azure в регионе Azure, а затем добавьте пример базы данных в группу отработки отказа.

> [!IMPORTANT]
> Для выполнения этих командлетов требуется Azure PowerShell версии 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Заполните переменные для сценариев PowerShell, используя значения для имеющегося сервера и примера базы данных, а также присвойте имени группы отработки отказа глобальное уникальное значение.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Создайте пустой резервный сервер в вашем регионе отработки отказа.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Создайте группу отработки отказа между двумя серверами.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Добавьте базу данных в группу отработки отказа.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Установка программного обеспечения Java

В этом разделе предполагается, что у вас уже есть опыт разработки на Java и вы только начали работу с Базой данных SQL Azure. 

### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать проект Java. Введите следующие команды для установки **brew** и **Maven**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Для получения подробных инструкций по установке и настройке сред Java и Maven перейдите на ресурс [создания приложения с помощью SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), выберите **Java**, затем — **MacOS** и выполните подробные инструкции по настройке Java и Maven (шаги 1.2 и 1.3).

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать проект Java. Введите следующие команды для установки **Maven**.

```bash
sudo apt-get install maven
```

Для получения подробных инструкций по установке и настройке сред Java и Maven перейдите на ресурс [создания приложения с помощью SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), выберите **Java**, затем — **Ubuntu** и выполните подробные инструкции по настройке Java и Maven (шаги 1.2, 1.3 и 1.4).

### <a name="windows"></a>**Windows**
Установите [Maven](https://maven.apache.org/download.cgi) с помощью официального установщика. Maven можно использовать для управления зависимостями, выполнения сборки, тестирования и запуска проекта Java. Для получения подробных инструкций по установке и настройке сред Java и Maven перейдите на ресурс [создания приложения с помощью SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), выберите **Java**, затем — Windows и выполните подробные инструкции по настройке Java и Maven (шаги 1.2 и 1.3).

## <a name="create-sqldbsample-project"></a>Создание проекта SqlDbSample

1. В окне командной консоли (например, Bash) создайте проект Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Введите **Y** и нажмите клавишу **ВВОД**.
3. Перейдите в только что созданный проект.

   ```bash
   cd SqlDbSamples
   ```

4. Используя любой удобный редактор, откройте файл pom.xml в папке проекта. 

5. Добавьте Microsoft JDBC Driver для зависимости SQL Server в проект Maven. Для этого откройте удобный текстовый редактор, скопируйте и вставьте следующие строки в файл pom.xml. Не перезаписывайте имеющиеся значения, предварительно заполненные в файле. JDBC-зависимость необходимо вставить в рамках большего раздела зависимостей.   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Укажите версию Java, с помощью которой нужно скомпилировать проект. Для этого добавьте следующий раздел свойств в файл pom.xml после раздела зависимостей. 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Добавьте следующий раздел сборки в файл pom.xml после раздела свойств для поддержки файлов манифеста в JAR-файлах.       

   ```xml
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
8. Сохраните и закройте файл pom.xml.
9. Откройте файл App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) и замените его содержимое на содержимое ниже. Замените имя группы отработки отказа на имя своей группы отработки отказа. Если вы изменили значения для имени базы данных, пользователя или пароля, измените также эти значения.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Сохраните и закройте файл App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Компиляция и выполнение проекта SqlDbSample

1. В окне командной консоли выполните следующую команду.

   ```bash
   mvn package
   ```
2. После завершения выполните следующую команду для запуска приложения (запуск выполняется около часа, если не завершить эту задачу вручную):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Выполнение отработки аварийного восстановления

1. Для группы отработки отказа вызовите отработку отказа вручную. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Просмотрите результаты приложения во время отработки отказа. Во время обновления кэша DNS некоторые операции вставки завершатся ошибкой.     

3. Узнайте, какую роль выполняет сервер аварийного восстановления.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Восстановите размещение.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Просмотрите результаты приложения во время восстановления размещения. Во время обновления кэша DNS некоторые операции вставки завершатся ошибкой.     

6. Узнайте, какую роль выполняет сервер аварийного восстановления.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого руководства вы научились настраивать базу данных SQL Azure и приложение для отработки отказа в удаленный регион, а затем тестировать план отработки отказа.  Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> * создавать пользователей базы данных и предоставлять им разрешения;
> * настраивать правила брандмауэра уровня базы данных;
> * создавать группу отработки отказа георепликации;
> * создавать и компилировать приложения Java для запроса базы данных SQL Azure;
> * выполнять отработку аварийного восстановления.

Перейдите к следующему руководству, чтобы узнать, как создать Управляемый экземпляр.

> [!div class="nextstepaction"]
>[Создание Управляемого экземпляра](sql-database-managed-instance-create-tutorial-portal.md)

