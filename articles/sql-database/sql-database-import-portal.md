---
title: "Импорт BACPAC-файла для создания базы данных SQL Azure | Документация Майкрософт"
description: "Создание базы данных SQL Azure посредством импорта существующего BACPAC-файла."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: e77a3ba82e7620e23441d9296ebed46d41cb525c


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Импорт BACPAC-файла для создания базы данных SQL Azure с помощью портала Azure

В этой статье приведены указания по созданию базы данных SQL Azure из BACPAC-файла с помощью [портала Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Предварительные требования

Чтобы импортировать базу данных SQL из BACPAC-файла, необходимо следующее:

* Подписка Azure. 
* Сервер базы данных SQL Azure версии&12;. Если у вас отсутствует сервер версии&12;, создайте его в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
* BACPAC-файл базы данных, которую нужно импортировать, в контейнере больших двоичных объектов [учетной записи хранения Azure ("Стандартный")](../storage/storage-create-storage-account.md) .

> [!IMPORTANT]
> При импорте BACPAC-файла из хранилища BLOB-объектов используйте хранилище уровня "Стандартный". Импорт BACPAC-файла из хранилища уровня "Премиум" не поддерживается.
> 

## <a name="import-the-database"></a>Импорт базы данных
Откройте колонку SQL Server:

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выберите **Серверы SQL**.
3. Выберите сервер, на который нужно восстановить базу данных.
4. В колонке SQL Server щелкните **Импорт базы данных**, чтобы открыть колонку **Импорт базы данных**.
   
   ![Импорт базы данных][1]
5. Щелкните **Хранилище** и выберите учетную запись хранения, контейнер больших двоичных объектов и BACPAC-файл и нажмите кнопку **ОК**.
   
   ![настроить параметры хранилища][2]
6. Выберите уровень цен для новой базы данных и нажмите кнопку **Выбрать**. Импорт базы данных напрямую в эластичный пул не поддерживается, но для начала можно импортировать ее как автономную базу данных, а потом переместить ее в пул.
   
   ![выберите ценовую категорию][3]
7. Введите **имя базы данных** , создаваемой из BACPAC-файла.
8. Выберите тип аутентификации и предоставьте данные аутентификации для сервера. 
9. Нажмите кнопку **Создать** , чтобы создать базу данных из BACPAC-файла.
   
   ![создание базы данных][4]

Нажмите кнопку **Создать** , чтобы отправить в службу запрос об импорте базы данных. Операция импорта может занять некоторое время в зависимости от размера базы данных.

## <a name="monitor-the-progress-of-the-import-operation"></a>Отслеживание хода выполнения операции импорта
1. Выберите **Серверы SQL**.
2. Выберите сервер, на который нужно восстановить базу данных.
3. В колонке SQL Server в области "Операции" щелкните **Журнал импорта и экспорта**.
   
   ![журнал импорта и экспорта][5]
   ![журнал импорта и экспорта][6]

4. Щелкните **Базы данных SQL**, чтобы проверить активность базы данных на сервере, и убедитесь, что для новой базы данных задано состояние **В сети**.

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы научиться подключаться к импортированной базе данных SQL и отправлять к ней запросы, ознакомьтесь со статьей [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md).
* Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-cloud-migrate.md).


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png



<!--HONumber=Feb17_HO2-->


