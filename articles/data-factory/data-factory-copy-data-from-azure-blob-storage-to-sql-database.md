---
title: "Копирование данных из хранилища BLOB-объектов в Базу данных SQL Azure | Документация Майкрософт"
description: "В этом учебнике рассказывается, как использовать действие копирования в конвейере фабрики данных Azure для копирования данных из хранилища BLOB-объектов в базу данных SQL."
keywords: "BLOB-объект, SQL, хранилище BLOB-объектов, копирование данных"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 2dcba235353f2b1bd2b42d93066a91071f0ea1a1
ms.openlocfilehash: 8b9afcd62ad318e181e2d210af58dcb412eefaaf
ms.lasthandoff: 02/22/2017


---
# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных
> [!div class="op_single_selector"]
> * [Обзор и предварительные требования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)
> * [Портал Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Шаблон Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [ИНТЕРФЕЙС REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

В этом учебнике вы создадите фабрику данных с конвейером, чтобы скопировать данные из хранилища BLOB-объектов в базу данных SQL.

Действие копирования перемещает данные в фабрике данных Azure. Это действие выполняется с помощью глобально доступной службы, обеспечивающей безопасное, надежное и масштабируемое копирование данных между разными хранилищами. Дополнительные сведения о действии копирования см. в статье [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md).  

> [!NOTE]
> Подробный обзор службы фабрики данных см. в статье [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](data-factory-introduction.md).
>
>

## <a name="prerequisites-for-the-tutorial"></a>Предварительные требования для прохождения этого учебника
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**.  Если у вас нет подписки, вы можете создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в статье [Бесплатная пробная версия](http://azure.microsoft.com/pricing/free-trial/).
* **исходного**хранилища данных. В этом учебнике в качестве **источника** будет использоваться хранилище BLOB-объектов. в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md#create-a-storage-account) .
* **База данных SQL Azure**. В этом учебнике используется база данных SQL Azure в качестве **конечного** хранилища данных. Если нет базы данных SQL Azure, которую можно использовать для изучения этого учебника, ознакомьтесь с разделом [Как создать и настроить базу данных SQL Azure](../sql-database/sql-database-get-started.md), чтобы создать такую базу данных.
* **SQL Server 2012/2014 или Visual Studio 2013**. Для создания образца базы данных и просмотра итоговых данных в базе данных используется SQL Server Management Studio или Visual Studio .  

## <a name="collect-blob-storage-account-name-and-key"></a>Получение имени и ключа учетной записи хранения BLOB-объектов
В ходе изучения этого учебника потребуются имя и ключ вашей учетной записи хранения Azure. Запишите **имя** и **ключ учетной записи** для своей учетной записи хранения Azure.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Больше служб** в меню слева и выберите **Учетные записи хранения**.

    !["Обзор" — "Учетные записи хранения"](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. В колонке **Учетные записи хранения** выберите **учетную запись хранения Azure**, которая будет использоваться в ходе изучения этого учебника.
4. В разделе **Параметры** выберите ссылку **Ключи доступа**.
5. Нажмите кнопку **Копировать** (изображение) рядом с текстовым полем **Имя учетной записи хранения**, вставьте скопированный текст, например, в текстовый файл, и сохраните его.
6. Повторите предыдущий шаг для ключа **key1**.

    ![Ключ доступа к хранилищу](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Закройте все колонки, нажав **X**.

## <a name="collect-sql-server-database-user-names"></a>Получение имени сервера SQL, имени базы данных и имени пользователя
В ходе изучения этого учебника потребуются имена сервера Azure SQL Server, базы данных и пользователя. Запишите имена **сервера**, **базы данных** и **пользователя** базы данных SQL Azure.

1. На **портале Azure** щелкните **Больше служб** слева и выберите **Базы данных SQL**.
2. В **колонке баз данных SQL** выберите **базу данных**, которую вы планируете использовать для этого учебника. Запишите **имя базы данных**.  
3. В колонке **База данных SQL** в разделе **Параметры** щелкните **Свойства**.
4. Запишите значения параметров **Имя сервера** и **Имя для входа администратора сервера**.
5. Закройте все колонки, нажав **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Предоставление службам Azure доступа к серверу SQL
Убедитесь, что параметр **Разрешить доступ к службам Azure** имеет состояние **ВКЛ** для вашего сервера Azure SQL Server, чтобы служба фабрики данных могла иметь доступ к серверу Azure SQL Server. Чтобы проверить и при необходимости включить этот параметр, сделайте следующее.

1. Щелкните **Больше служб** слева и выберите **Серверы SQL**.
2. Выберите сервер и щелкните **Брандмауэр** в разделе **Параметры**.
3. В колонке **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.
4. Закройте все колонки, нажав **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Подготовка хранилища BLOB-объектов и базы данных SQL
Теперь подготовьте хранилище больших двоичных объектов Azure и базу данных SQL Azure к изучению этого учебника, выполнив следующие действия.  

1. Запустите блокнот, вставьте следующий текст и сохраните файл на жестком диске под именем **emp.txt** в папке **C:\ADFGetStarted**.

    ```
    John, Doe
    Jane, Doe
    ```
2. При помощи таких инструментов, как [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и передайте файл **emp.txt** в этот контейнер.

    ![Обозреватель хранилищ Azure Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Используйте следующий скрипт SQL, чтобы создать таблицу **emp** в базе данных SQL Azure.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Если на вашем компьютере установлен SQL Server 2012 или 2014**, выполните инструкции в статье [Управление базой данных SQL Azure с помощью SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md), чтобы подключиться к серверу Azure SQL Server и запустить скрипт SQL. В этой статье для настройки брандмауэра сервера Azure SQL Server используется [классический портал Azure](http://manage.windowsazure.com), а не [новый портал Azure](https://portal.azure.com).

    Если клиенту не разрешен доступ к серверу Azure SQL Server, то следует настроить брандмауэр вашего сервера Azure SQL Server, чтобы разрешить доступ с вашей машины (IP-адрес). В [этой статье](../sql-database/sql-database-configure-firewall-settings.md) описано, как настроить брандмауэр для сервера Azure SQL Server.

Необходимые условия выполнены. Для создания фабрики данных можно использовать один из приведенных ниже способов. Выберите в раскрывающемся списке в верхней части страницы один из вариантов или щелкните одну из следующих ссылок, чтобы изучить руководство.     

* [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)
* [Портал Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Шаблон Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [ИНТЕРФЕЙС REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Описанный в этом руководстве конвейер данных копирует данные из исходного хранилища данных в целевое. Он не преобразовывает входные данные в выходные. Инструкции по преобразованию данных с помощью фабрики данных Azure см. в [учебнике по созданию первого конвейера для преобразования данных с помощью кластера Hadoop](data-factory-build-your-first-pipeline.md).
> 
> Можно объединить в цепочку два действия (выполнить одно действие вслед за другим), настроив выходной набор данных одного действия как входной набор данных другого действия. Подробные сведения см. в статье [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md). 
