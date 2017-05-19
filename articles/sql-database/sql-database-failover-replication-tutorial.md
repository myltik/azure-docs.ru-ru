---
title: "Репликация и отработка отказа решения базы данных SQL Azure | Документация Майкрософт"
description: "Сведения о настройке базы данных SQL Azure и приложения для отработки отказа в реплицированную базу данных и тестовой отработки отказа."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>Репликация и отработка отказа решения базы данных SQL Azure

В этом руководстве вы настроите базу данных SQL Azure и приложение для отработки отказа в удаленный регион, а затем протестируете план отработки отказа. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

В рамках этого руководства вам потребуются:

- Последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). При установке SSMS также устанавливается последняя версия SqlPackage, служебной программы командной строки, используемой для автоматизации ряда задач по разработке базы данных. 
- База данных Azure для переноса. В этом руководстве используется пример базы данных AdventureWorksLT с именем **mySampleDatabase из одного из этих кратких руководств:

   - [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
   - [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>Создание пользователей Azure Active Directory (необязательно)

На этом шаге вы создадите или определите пользователей Azure Active Directory, чтобы добавить их в качестве пользователей логического сервера базы данных SQL Azure и примера базы данных.
- Если ваша подписка является частью корпоративной среды Azure Active Directory с существующими учетными записями пользователей, определите 3 учетные записи пользователей для администратора Active Directory, администратора приложения и пользователя приложения в этом руководстве и перейдите к созданию имен входа и пользователей базы данных SQL. 
- Если подписка не является частью корпоративной среды Azure Active Directory или является частью корпоративной среды Azure Active Directory без существующих учетных записей пользователей (и у вас есть разрешение на создание новых учетных записей пользователей Azure Active Directory).

1. Войдите на [портал Azure](http://portal.azure.com).
2. В меню слева щелкните **Больше служб**.
3. В поле фильтра введите **Azure**, а затем выберите **Azure Active Directory**.
4. В области **Быстрые задачи** на странице **Azure Active Directory** щелкните **Добавить пользователя**.
5. В форме **Пользователь** создайте следующего пользователя.
   - Имя: **ad-admin**.
   - Имя пользователя: **ad-admin@yourdomain** (Yopu4708).
6. Установите флажок **Показать пароль** и запишите пароль для этой учетной записи пользователя, чтобы использовать его в последующем.
7. Щелкните **Создать**.
8. Повторите 3 предыдущих шага, чтобы создать еще 2 пользователей.
   - Имя: **app-admin**.
   - Имя пользователя: **app-admin@yourdomain** (Buju4319).
   - Имя: **app-user**.
   - Имя пользователя: **app-user@yourdomain** (Nonu4001).

9. Откройте новое окно браузера и войдите на портал Azure, используя созданную учетную запись **ad-admin**.
10. На странице **Смените пароль** введите созданный системой пароль в поле **Текущий пароль**. 
11. В полях **Новый пароль** и **Подтвердите пароль** введите пароль по вашему выбору.
12. Щелкните **Сменить пароль и войти в систему**.

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>Настройка интеграции базы данных SQL с Azure Active Directory

1. В меню слева щелкните **Больше служб**, введите **sql** в текстовом поле фильтра, а затем выберите **Серверы SQL Server**.
2. На странице **Серверы SQL Server** щелкните ваш сервер базы данных SQL.
3. В области "Основное" на странице **Обзор** вашего сервера щелкните **Не настроено** в разделе **Администратор Active Directory**.
4. На странице **Администратор Active Directory** щелкните **Задать администратора**.
5. Выберите учетную запись Azure Active Directory **ad-admin** (или другую существующую учетную запись, например собственную) в качестве администратора сервера для сервера базы данных SQL Azure.
6. Нажмите кнопку **Выбрать**.
7. Щелкните **Сохранить**.

## <a name="create-users-with-permissions-for-your-database"></a>Создание пользователей базы данных с разрешениями

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

Используйте SQL Server Management Studio для подключения к базе данных и создания учетных записей пользователей. Эти учетные записи пользователей автоматически реплицируются на сервер-получатель. Вам может потребоваться настроить правило брандмауэра при подключении из клиента по IP-адресу, для которого еще не настроен брандмауэр. Пошаговые инструкции см. в статье [Создание базы данных SQL Azure на портале Azure](sql-database-get-started-portal.md).

1. Откройте среду SQL Server Management Studio.
2. Измените режим **проверки подлинности** на **Проверка пароля Active Directory**.
3. Подключитесь к серверу, используя созданную учетную запись администратора сервера Azure Active Directory. 
4. В обозревателе объектов разверните **Системные базы данных**, щелкните правой кнопкой мыши **mySampleDatabase** и выберите пункт **Новый запрос**.
5. В окне запроса выполните следующий запрос, чтобы создать учетные записи пользователей в базе данных, предоставив разрешения **db_owner** двум учетным записям администраторов. Замените соответствующий заполнитель именем вашего домена.

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>Создание брандмауэра на уровне базы данных

Используйте SQL Server Management Studio, чтобы создать для базы данных правило брандмауэра на уровне базы данных. Это правило автоматически реплицируется на сервер-получатель. В целях тестирования можно создать правило брандмауэра для всех IP-адресов (0.0.0.0 и 255.255.255.255), правило брандмауэра для одного IP-адреса, с помощью которого вы создали правило брандмауэра сервера, или настроить одно или несколько правил брандмауэра для IP-адресов компьютеров, которые вы хотите использовать для тестирования в этом руководстве.  

- В открытом окне запроса замените предыдущий запрос следующим запросом, указав соответствующие IP-адреса для вашей среды. 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>Создание группы отработки отказа 

Выберите регион для отработки отказа, создайте пустой сервер в этом регионе, а затем создайте группу отработки отказа между существующим сервером и новым пустым сервером.

1. Заполните переменные.

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. Создайте пустой резервный сервер в вашем регионе отработки отказа.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. Создайте группу отработки отказа.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. Добавьте базу данных в группу отработки отказа.

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>Добавьте пустой сервер резервного копирования к домену.

1. На портале Azure щелкните **Больше служб** в меню слева, введите **sql** в текстовое поле фильтра, а затем выберите **Серверы SQL Server**.
2. На странице **Серверы SQL Server** щелкните новый сервер аварийного восстановления базы данных SQL.
3. В области "Основное" на странице **Обзор** вашего сервера щелкните **Не настроено** в разделе **Администратор Active Directory**.
4. На странице **Администратор Active Directory** щелкните **Задать администратора**.
5. Выберите учетную запись Azure Active Directory **ad-admin** (или другую существующую учетную запись, например собственную) в качестве администратора сервера для нового сервера аварийного восстановления базы данных SQL Azure.
6. Нажмите кнопку **Выбрать**.
7. Щелкните **Сохранить**.

## <a name="prepare-client-tier"></a>Подготовка клиентского уровня

1. Создайте профиль TM с профилем отработки отказа AWProfile.
2. Настройка мониторинга

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>Развертывание приложения Java и подключение к базе данных

<In progress> См. сведения в статье [База данных SQL Azure: подключение и запрос данных с помощью Java](sql-database-connect-query-java.md).

<TO DO: change user to app-user>

1. Установите Java 8.
2. Установите Maven.
3. Создайте проект Maven.
4. Добавьте следующий код в файл pom.xml. 

   - Dependency

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - Уровень языка:

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - Параметр сборки для поддержки файлов манифеста в JAR-файлах:

      ```java
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
5. В файл App.java добавьте следующее:

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

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
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

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
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

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
6. Сохраните файл.

## <a name="compile-and-run"></a>Компиляция и запуск

1. Перейдите в консоль и выполните команду:

   ```java
   mvn package
   ```
2. После завершения выполните команду для запуска приложения (оно будет выполняться около 1 часа, если его не остановить вручную).

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   В случае успешного выполнения выходные данные будут выглядеть примерно следующим образом:

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful

## <a name="perform-dr-drill"></a>Тестирование аварийного восстановления

1. Вызовите принудительную отработку отказа группы отработки отказа вручную. Если потеря данных во время тестирования аварийного восстановления недопустима, следует удалить параметр -AllowDataLoss.

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    Отключите основную конечную точку в профиле TM (для запуска отработки отказа конечной точки). 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Перезапустите приложение.


## <a name="relocate-application-to-primary-region"></a>Перенос приложения в основной регион

1.    Вызовите адаптированную отработку отказа группы отработки отказа вручную. Не указывайте параметр -AllowDataLoss.

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    Отключите дополнительную конечную точку (webapp2ep) в профиле TM (для запуска отработки отказа конечной точки). 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Перезапустите приложение.

## <a name="troubleshoot-failover"></a>Устранение неполадок отработки отказа 

Узнайте, какой регион является основным, чтобы убедиться, что отработка отказа выполнена. Этот регион можно узнать по роли (основная или дополнительная).

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>Дальнейшие действия 

- Сходите в кинотеатр недалеко от вас.
