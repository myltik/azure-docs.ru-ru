---
title: "Включение базы данных Stretch для базы данных | Документация Майкрософт"
description: "Узнайте, как настроить базу данных для растяжения базы данных."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 55fc4142-4be9-4664-8ea9-48a5e177838f
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6d3a81c3c919e50c123cef00dfb3e4a5ab23a44


---
# <a name="enable-stretch-database-for-a-database"></a>Включение растяжения базы данных для базы данных
Чтобы настроить имеющуюся базу данных для использования базы данных Stretch, выберите **Задачи | Stretch | Включить** для базы данных в SQL Server Management Studio. После этого откроется мастер **включения Stretch для базы данных**. Чтобы включить базу данных Stretch для базы данных, также можно использовать Transact\-SQL.

Если при выборе **Задачи &gt; Перенос &gt; Включить** для отдельной таблицы база данных Stretch для базы данных еще не включена, то мастер сначала настроит базу данных для использования базы данных Stretch и позволит выбрать таблицы в ходе этого процесса. В этом случае следуйте указаниям в этом разделе вместо действий, описанных в разделе [Включение растяжения базы данных для таблицы](sql-server-stretch-database-enable-database.md).

Чтобы включить базу данных Stretch для базы данных или таблицы, требуются разрешения db\_owner. Для включения растяжения базы данных для базы данных также требуются разрешения CONTROL DATABASE.

> [!NOTE]
> В случае последующего отключения базы данных Stretch для таблицы или базы данных помните, что такое отключение не приводит к удалению дистанционного объекта. Если вы хотите удалить удаленную таблицу или базу данных, ее необходимо удалить с помощью портала управления Azure. За хранение дистанционных объектов в Azure по-прежнему будет взиматься плата, пока вы не удалите их вручную.
> 
> 

## <a name="before-you-get-started"></a>Необходимые условия
* Перед настройкой базы данных для растяжения рекомендуется запустить Помощник по растяжению базы данных для определения баз данных и таблиц, к которым можно применить растяжение. Помощник по растяжению баз данных также определит критические препятствия. Дополнительные сведения см. в статье [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (Определение баз данных и таблиц для базы данных Stretch).
* Ознакомьтесь со статьей [Ограничения контактной зоны и критические препятствия для использования базы данных Stretch](sql-server-stretch-database-limitations.md).
* При растяжении баз данных выполняется перенос данных в Azure. Поэтому необходимо иметь учетную запись Azure и подписку для выставления счетов. Для получения учетной записи Azure щелкните [здесь](http://azure.microsoft.com/pricing/free-trial/).
* Подготовьте данные подключения и имени для входа, необходимые для создания нового сервера Azure, или выберите существующий сервер Azure.

## <a name="a-nameenabletsqlserveraprerequisite-enable-stretch-database-on-the-server"></a><a name="EnableTSQLServer"></a>Необходимое условие: включение базы данных Stretch на сервере
Перед включением растяжения базы данных для базы данных или таблицы необходимо включить его на локальном сервере. Эта операция требует разрешений sysadmin или serveradmin.

* При наличии необходимых разрешений администратора мастер **Включение базы данных Stretch** настроит сервер для базы данных Stretch.
* Если у вас нет необходимых разрешений, администратор должен вручную включить эту возможность, выполнив **sp\_configure** перед запуском мастера, или должен запустить мастер самостоятельно.

Чтобы включить базу данных Stretch на сервере вручную, выполните **sp\_configure** и включите параметр **Удаленный архив данных**. В следующем примере включается параметр **Архив удаленных данных** (для него задается значение 1).

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Дополнительные сведения см. в статьях [Настройка параметра конфигурации сервера remote data archive](https://msdn.microsoft.com/library/mt143175.aspx) и [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="a-namewizardause-the-wizard-to-enable-stretch-database-on-a-database"></a><a name="Wizard"></a>Включение базы данных Stretch для базы данных с помощью мастера
Сведения о мастере включения базы данных Stretch, включая информацию, которую необходимо ввести, и варианты, которые нужно выбрать, приведены в разделе [Get started by running the Enable Database for Stretch Wizard](sql-server-stretch-database-wizard.md)(Начало работы с запуска мастера "Включение базы данных Stretch").

## <a name="a-nameenabletsqldatabaseause-transact-sql-to-enable-stretch-database-on-a-database"></a><a name="EnableTSQLDatabase"></a>Включение базы данных Stretch для базы данных с помощью Transact\-SQL
Перед включением растяжения базы данных для отдельных таблиц необходимо включить его для базы данных.

Чтобы включить базу данных Stretch для базы данных или таблицы, требуются разрешения db\_owner. Для включения растяжения базы данных для базы данных также требуются разрешения CONTROL DATABASE.

1. Прежде чем начать, выберите существующий или создайте новый сервер Azure для данных, которые будут перенесены базой данных Stretch.
2. На сервере Azure создайте правило брандмауэра с диапазоном IP-адресов сервера SQL Server. Это правило позволит SQL Server обмениваться данными с удаленным сервером.
   
   Вы можете быстро найти требуемые значения, а также создать правило брандмауэра, попытавшись подключиться к серверу Azure из обозревателя объектов в SQL Server Management Studio (SSMS). SSMS поможет вам создать правило, открыв следующее диалоговое окно, которое уже содержит необходимые значения IP-адресов.
   
   ![Создание правила брандмауэра в SSMS][FirewallRule]
3. Для настройки растяжения базы данных сервера SQL Server у базы данных должен быть главный ключ базы данных. Главный ключ базы данных защищает учетные данные, используемые для подключения к удаленной базе данных, использующей растяжение базы данных. Ниже приведен пример, который создает новый главный ключ базы данных.
   
   ```tsql
   USE <database>;
   GO
   
   CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
   GO
   ```
   
   Дополнительные сведения о главном ключе базы данных см. в статьях [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) и [Создание главного ключа базы данных](https://msdn.microsoft.com/library/aa337551.aspx).
4. При настройке базы данных для растяжения необходимо предоставить учетные данные, которые будут использоваться для обмена данными между локальным SQL Server и удаленным сервером Azure. Это можно сделать двумя способами.
   
   * Укажите учетные данные администратора.
     
     * Если вы включаете растяжение с помощью мастера, можно указать учетные данные с помощью мастера.
     * Если вы собираетесь включить базу данных Stretch, используя инструкцию **ALTER DATABASE**, вам нужно создать учетные данные вручную, прежде чем запускать инструкцию **ALTER DATABASE**.
     
     Ниже приведен пример, который создает новые учетные данные.
     
     ```tsql
     CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
         WITH IDENTITY = '<identity>' , SECRET = '<secret>';
     GO
     ```
     
     Дополнительные сведения об учетных данных см. в статье [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Для создания учетных данных необходимы разрешения ALTER ANY CREDENTIAL.
   * Для взаимодействия сервера SQL Server с удаленным сервером Azure можно использовать учетную запись службы федерации при выполнении всех условий, перечисленных ниже.
     
     * Учетная запись службы, под которой выполняется экземпляр SQL Server, является учетной записью домена.
     * Учетная запись домена принадлежит к тому домену, каталог Active Directory которого включен в федерацию с Azure Active Directory.
     * Удаленный сервер Azure настроен для поддержки проверки подлинности Azure Active Directory.
     * Учетная запись службы, под которой выполняется экземпляр SQL Server, должна быть настроена как dbmanager или sysadmin на удаленном сервере Azure.
5. Для настройки растяжения базы данных для базы данных выполните инструкцию ALTER DATABASE.
   
   1. В качестве аргумента SERVER укажите имя имеющегося сервера Azure, включая часть `.database.windows.net` имени \-, например `MyStretchDatabaseServer.database.windows.net`.
   2. Укажите имеющиеся учетные данные администратора в аргументе CREDENTIAL или задайте для параметра FEDERATED\_SERVICE\_ACCOUNT значение ON. В примере ниже приведены существующие учетные данные.
   
   ```tsql
   ALTER DATABASE <database name>
       SET REMOTE_DATA_ARCHIVE = ON
           (
               SERVER = '<server_name>',
               CREDENTIAL = <db_scoped_credential_name>
           ) ;
   GO
   ```

## <a name="next-steps"></a>Дальнейшие действия
* [Включение растяжения базы данных для таблицы](sql-server-stretch-database-enable-table.md) .
* [отслеживайте базу данных Stretch](sql-server-stretch-database-monitor.md) .
* [Приостановите и возобновите растяжение баз данных](sql-server-stretch-database-pause.md)
* [Управляйте растяжением баз данных и устраняйте неполадки](sql-server-stretch-database-manage.md)
* [Резервное копирование баз данных с поддержкой растяжения](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Дополнительные материалы
[Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md)

[Параметры ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png



<!--HONumber=Nov16_HO3-->


