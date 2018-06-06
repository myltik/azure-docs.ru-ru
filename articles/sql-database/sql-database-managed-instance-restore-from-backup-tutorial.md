---
title: Восстановление резервной копии в Управляемый экземпляр Базы данных SQL Azure | Документация Майкрософт
description: Восстановление резервной копии базы данных в Управляемый экземпляр Базы данных SQL Azure с помощью SSMS.
keywords: sql database tutorial, sql database managed instance, restore a backup
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "31426208"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Восстановление резервной копии базы данных в Управляемый экземпляр Базы данных SQL Azure

В этом руководстве описано, как восстановить резервную копию базы данных, хранящуюся в хранилище больших двоичных объектов, в управляемый экземпляр с помощью файла резервной копии WideWorldImporters-Standard. Использование этого метода сопряжено с некоторым простоем. Сведения об использовании Azure Database Migration Service (DMS) для миграции см. в статье о [миграции SQL Server в управляемый экземпляр базы данных SQL Azure](../dms/tutorial-sql-server-to-managed-instance.md). См. дополнительные сведения о [переносе экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Загрузка Wide World Importers — стандартного файла резервной копии
> * Создание учетной записи хранения Azure и передача файла резервной копии
> * Восстановление базы данных Wide World Importers из файла резервной копии

## <a name="prerequisites"></a>предварительным требованиям

В этом руководстве в качестве начальной точки используются ресурсы, созданные в руководстве по [созданию Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-create-tutorial-portal.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Загрузка Wide World Importers — стандартного файла резервной копии

Чтобы загрузить Wide World Importers — стандартный файл резервной копии, выполните шаги ниже.

В Internet Explorer введите в поле URL-адреса https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak и при появлении запроса щелкните **Сохранить**, чтобы сохранить этого файла в папку **Загрузки**.

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Создание учетной записи хранения Azure и передача файла резервной копии

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Найдите **Хранилище**, а затем щелкните **Учетная запись хранения**, чтобы открыть форму учетной записи хранения.

   ![запись хранения Azure](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Укажите запрошенные сведения в форме учетной записи хранения, используя данные в таблице ниже.

   | Параметр| Рекомендуемое значение | Описание |
   | ------ | --------------- | ----------- |
   |**Имя**|Любое допустимое имя|Сведения о допустимых именах см. в статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Модель развертывания**|Модель ресурсов||
   |**Account kind** (Тип учетной записи)|Хранилище BLOB-объектов||
   |**Производительность**|Standard или Premium|Магнитные диски или диски SSD.|
   |**Репликация**|Локально избыточное хранилище||
   |\*\*Уровень доступа (по умолчанию)|"Холодный" или "горячий"||
   |**Secure transfer required** (Требуется безопасное перемещение)|Отключено||
   |**Подписка**|Ваша подписка|Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions).|
   |**Группа ресурсов**|Группа ресурсов, созданная ранее|| 
   |**Местоположение.**|Ранее выбранное расположение||
   |**Виртуальные сети**|Отключено||

4. Нажмите кнопку **Создать**.

   ![сведения об учетной записи хранения](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. После успешного развертывания учетной записи хранения откройте новую учетную запись хранения.
6. В разделе **Параметры** щелкните **Подписанный URL-адрес**, чтобы открыть форму подписанного URL-адреса (SAS).

   ![форма SAS](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. При необходимости измените в форме SAS значения по умолчанию. Обратите внимание, что по умолчанию дата и время истечения срока действия составляет всего 8 часов.
8. Нажмите кнопку **Создать SAS**.

   ![завершение создания формы SAS](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Скопируйте и сохраните **маркер SAS** и **подписанный URL-адрес сервера больших двоичных объектов**.
10. В разделе **Параметры** щелкните **Контейнеры**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Нажмите кнопку **+ Контейнер**, чтобы создать контейнер для хранения файла резервной копии.
12. Укажите запрошенные сведения в форме контейнера, используя данные в таблице ниже.

    | Параметр| Рекомендуемое значение | Описание |
   | ------ | --------------- | ----------- |
   |**Имя**|Любое допустимое имя|Сведения о допустимых именах см. в статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Уровень общего доступа**|Закрытый (отсутствие анонимного доступа)||

    ![сведения о контейнере](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Последовательно выберите **ОК**.
14. После создания контейнера откройте его.

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. Щелкните **Свойства контейнера**, а затем скопируйте URL-адрес контейнера.

    ![URL-адрес контейнера](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Щелкните **Отправить**, чтобы открыть форму **Отправить BLOB-объект**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Перейдите в папку "Загрузки" и выберите файл **WideWorldIimporters-Standard.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Щелкните **Отправить**.
19. Подождите завершения отправки.

    ![завершение отправки](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Восстановление базы данных Wide World Importers из файла резервной копии

С помощью SSMS выполните следующие шаги для восстановления базы данных Wide World Importers в управляемый экземпляр из файла резервной копии.

1. Откройте в SSMS окно нового запроса.
2. Используйте сценарий ниже для создания учетных данных SAS. Введите URL-адрес контейнера учетной записи хранения и ключ SAS, как указано.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. Используйте сценарий ниже для проверки учетных данных SAS и срока действия резервной копии. Укажите URL-адрес контейнера, в котором находится файл резервной копии.

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![список файлов](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Используйте сценарий ниже для восстановления базы данных Adventure Works 2012 из файла резервной копии. Укажите URL-адрес контейнера, в котором находится этот файл.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![выполнение восстановления](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Чтобы отслеживать состояние восстановления, выполните следующий запрос в новом сеансе запроса:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![завершение восстановления в процентах](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. По завершении восстановления просмотрите восстановленную базу данных в обозревателе объектов. 

    ![полное завершение восстановления](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как восстановить резервную копию базы данных, хранящуюся в хранилище больших двоичных объектов, в управляемый экземпляр с помощью файла резервной копии WideWorldImporters-Standard. Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> * Загрузка Wide World Importers — стандартного файла резервной копии
> * Создание учетной записи хранения Azure и передача файла резервной копии
> * Восстановление базы данных Wide World Importers из файла резервной копии

Переходите к следующему руководству, чтобы узнать, как перенести SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS.

> [!div class="nextstepaction"]
>[Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS](../dms/tutorial-sql-server-to-managed-instance.md)