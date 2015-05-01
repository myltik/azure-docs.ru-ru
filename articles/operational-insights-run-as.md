<properties 
   pageTitle="Учетные записи запуска от имени в Operations Manager для службы Operational Insights"
   description="Узнайте, как настроить учетные записи запуска от имени в Operations Manager, используемые в службе Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Учетные записи запуска от имени в Operations Manager для службы Operational Insights

Microsoft Azure Operational Insights использует агент Operations Manager и группу управления для сбора и отправки данных в службу. С целью предоставления ценных услуг служба Operational Insights строится на пакетах управления для рабочих нагрузок. Каждой рабочей нагрузке требуются права, относящиеся к конкретным рабочим нагрузкам, для запуска пакетов управления в другом контексте безопасности, например в учетной записи домена. Необходимо предоставить учетные данные, настроив учетную запись запуска от имени для Operations Manager.

В следующих разделах содержатся сведения о настройке учетных записей запуска от имени для Operations Manager для следующих рабочих нагрузок:

- Оценка SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

## Настройка учетной записи запуска от имени для оценки SQL

 Если пакет управления SQL Server уже используется, следует использовать эту учетную запись запуска от имени.

### Настройка учетной записи SQL запуска от имени в консоли управления

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени** щелкните **Профили** и откройте **Профиль запуска от имени для оценки SQL в Operational Insights**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая содержит учетные данные, необходимые для SQL Server, или нажмите кнопку **Создать** для ее создания.
	>[AZURE.NOTE] Тип учетной записи "Запуск от имени" должен быть указан как Windows. Учетная запись запуска от имени также должна входить в группу локальных администраторов на всех серверах Windows Server, на которых размещены экземпляры SQL Server.

5. Щелкните **Сохранить**.

6. Измените, а затем выполните следующий пример T-SQL на каждом экземпляре SQL Server, чтобы предоставить минимальные разрешения, необходимые учетной записи запуска от имени для выполнения оценки SQL. Но это не требуется делать в том случае, если учетная запись запуска от имени уже является частью серверной роли sysadmin в экземплярах SQL Server.

---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master
    
    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Настройка учетной записи SQL запуска от имени с помощью Windows PowerShell

Откройте окно PowerShell и выполните следующий скрипт после его обновления с собственными данными.

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Настройка учетной записи запуска от имени для Virtual Machine Manager

Убедитесь, что учетная запись запуска от имени имеет права для следующих действий:

- использование модуля VMM Windows;

- отправка запроса к базе данных VMM;

- удаленное администрирование агентов VMM, работающих на узлах виртуализации.

Выполните следующие действия, чтобы настроить учетную запись при подключении Operational Insights к Operations Manager.

### Настройка учетных данных для VMM

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени**, щелкните **Профили** и откройте **Учетная запись VMM запуска от имени для Operational Insights**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая содержит учетные данные, необходимые для  VMM, или нажмите кнопку **Создать** для ее создания.
	>[AZURE.NOTE] Тип учетной записи "Запуск от имени" должен быть указан как Windows.

5. Щелкните **Сохранить**.


## Настройка учетной записи запуска от имени для Lync Server

 Учетная запись запуска от имени должна входить в группу локальных администраторов и группу безопасности Lync RTCUniversalUserAdmins.

### Настройка учетных данных для учетной записи Lync

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени**, щелкните **Профили** и откройте **Учетная запись Lync запуска от имени для Operational Insights**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая входит в группу локальных администраторов и группу безопасности Lync RTCUniversalUserAdmins. 
	>[AZURE.NOTE] Тип учетной записи "Запуск от имени" должен быть указан как Windows.

5. Щелкните **Сохранить**.


## Настройка учетной записи запуска от имени для SharePoint


### Настройка учетных данных для учетной записи SharePoint

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени**, щелкните **Профили** и откройте **Учетная запись SharePoint запуска от имени для Operational Insights**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая содержит учетные данные, необходимые для SharePoint, или нажмите кнопку **Создать** для ее создания. 
	>[AZURE.NOTE] Тип учетной записи "Запуск от имени" должен быть указан как Windows.

5. Щелкните **Сохранить**.



<!--HONumber=52-->