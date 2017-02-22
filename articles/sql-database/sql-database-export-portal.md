---
title: "Портал Azure: экспорт базы данных SQL Azure в BACPAC-файл | Документация Майкрософт"
description: "Экспорт базы данных SQL Azure в BACPAC-файл с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Экспорт базы данных SQL Azure в BACPAC-файл с помощью портала Azure

Эта статья содержит инструкции по экспорту базы данных SQL Azure в BACPAC-файл, сохраняемый в хранилище BLOB-объектов Azure, с помощью [портала Azure](https://portal.azure.com). Общие сведения об экспорте в BACPAC-файл см. в [этой статье](sql-database-export.md).

> [!NOTE]
> Вы также можете экспортировать файл базы данных SQL Azure в BACPAC-файл с помощью [SQL Server Management Studio](sql-database-export-ssms.md), [PowerShell](sql-database-export-powershell.md) или [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо следующее:

* Подписка Azure.
* База данных Azure SQL. 
* [Учетная запись хранения Azure уровня "Стандартный"](../storage/storage-create-storage-account.md) с контейнером больших двоичных объектов для хранения BACPAC-файла в хранилище уровня "Стандартный".

## <a name="export-your-database"></a>Экспорт базы данных

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Откройте колонку "База данных SQL" для базы данных, которую нужно скопировать.
3. Убедитесь, что во время экспорта не будут выполняться транзакции. 

   > [!IMPORTANT]
   > Чтобы получить транзакционно согласованный BACPAC-файл, лучше всего [создать копию базы данных](sql-database-copy.md), а затем экспортировать эту копию. 
   > 

4. Нажмите **Базы данных SQL**.
5. Выберите базу данных для архивирования.
6. В колонке "База данных SQL" щелкните **Экспорт**, чтобы открыть колонку **Экспорт базы данных**.
   
   ![кнопка экспорта][1]
7. Щелкните **Хранилище** и выберите учетную запись хранения и контейнер больших двоичных объектов для хранения BACPAC-файла.
   
   ![Экспорт базы данных][2]
8. Выберите тип проверки подлинности. 
9. Введите правильные учетные данные для проверки подлинности на сервере Azure SQL, на котором находится экспортируемая база данных.
10. Нажмите кнопку **ОК**, чтобы экспортировать базу данных. После нажатия кнопки **ОК** создается запрос на экспорт базы данных и отправляется службе. Продолжительность экспорта зависит от размера и сложности базы данных и вашего уровня обслуживания. 
11. Просмотрите полученное уведомление.
   
   ![уведомление об экспорте][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Отслеживание хода выполнения операции экспорта
1. Выберите **Серверы SQL**.
2. Выберите сервер, содержащий первоначальную (исходную) базу данных, которую вы заархивировали.
3. Прокрутите вниз до раздела "Операции".
4. В колонке SQL Server нажмите **Журнал импорта и экспорта**:
   
   ![журнал импорта и экспорта][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Убедитесь, что BACPAC-файл находится в контейнере хранилища.
1. Щелкните элемент **Учетные записи хранения**.
2. Выберите учетную запись хранения, в которой сохранили BACPAC-файл.
3. Нажмите **Контейнеры** и выберите контейнер, в который экспортировали базу данных, для получения подробных сведений о резервной копии (из этого контейнера можно загрузить и сохранить BACPAC-файл).
   
   ![сведения о файле .bacpac][5]    

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о длительном периоде удержания резервных копий базы данных SQL Azure как альтернативе экспорту базы данных для создания архива см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Server, ознакомьтесь с разделом [Импорт файла BACPAC для создания новой пользовательской базы данных](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


