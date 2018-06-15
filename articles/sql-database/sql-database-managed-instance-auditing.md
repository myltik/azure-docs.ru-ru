---
title: Аудит управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: Узнайте, как приступить к аудиту управляемого экземпляра Базы данных SQL Azure с помощью T-SQL
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 71929be456de4b798da48bb202969deb71e1c371
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648859"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Приступая к аудиту управляемого экземпляра Базы данных SQL Azure

Аудит [управляемого экземпляра Базы данных SQL](sql-database-managed-instance.md) отслеживает события базы данных и сохраняет их в журнал аудита в учетной записи хранения Azure. Аудит также дает следующие возможности:
- Аудит может помочь вам соблюсти требования нормативов, проанализировать работу с базой данных и получить представление о расхождениях и аномалиях, которые могут указывать на бизнес-проблемы или предполагаемые нарушения безопасности.
- Средства аудита способствуют соблюдению стандартов соответствия, но не гарантируют их выполнение. Дополнительную информацию о программах Azure, поддерживающих проверку соблюдения стандартов, см. в [Центре управления безопасностью Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Настройка аудита для сервера

В следующем разделе описывается настройка аудита для управляемого экземпляра.
1. Перейдите на [портал Azure](https://portal.azure.com).
2. С помощью следующих шагов вы создадите **контейнер** службы хранилища Azure для хранения журналов аудита.

   - Перейдите к хранилищу Azure, в котором вы намерены хранить журналы аудита.

     > [!IMPORTANT]
     > Используйте учетную запись хранения из же региона, где размещен сервер управляемого экземпляра, чтобы избежать операций чтения и записи между регионами.

   - На странице учетной записи хранения перейдите на вкладку **Обзор** и щелкните **BLOB-объекты**.

     ![Область навигации][1]

   - В верхнем меню щелкните **+ Контейнер**, чтобы создать новый контейнер.

     ![Область навигации][2]

   - Укажите **имя** контейнера, установите **закрытый** уровень общего доступа и щелкните **ОК**.

     ![Область навигации][3]

   - В списке контейнеров щелкните только что созданный контейнер, а затем **Свойства контейнера**.

     ![Область навигации][4]

   - Скопируйте URL-адрес контейнера, щелкнув значок копирования, и сохраните этот URL-адрес (например, в Блокноте) для дальнейшего использования. URL-адрес контейнера должен иметь формат `https://<StorageName>.blob.core.windows.net/<ContainerName>`.

     ![Область навигации][5]

3. С помощью следующих действий можно создать **маркер SAS** службы хранилища Azure, который предоставляет права доступа к учетной записи хранения для аудита управляемого экземпляра.

   - Перейдите к учетной записи хранения Azure, в которой вы создали контейнер на предыдущем шаге.

   - Щелкните **Подписанный URL-адрес** в меню "Параметры хранилища".

     ![Область навигации][6]

   - Настройте SAS следующим образом.
     - **Допустимые службы**: большой двоичный объект
     - **Дата начала**: чтобы избежать проблем, связанных с часовыми поясами, выберите вчерашний день.
     - **Дата окончания**: выберите дату окончания срока действия для этого маркера SAS. 

       > [!NOTE]
       > Чтобы избежать сбоев аудита, не забудьте возобновить маркер по истечении срока действия.

     - Нажмите кнопку **Создать SAS**.

       ![Область навигации][7]

   - Когда вы щелкнете "Создать SAS", в нижней части страницы появится маркер SAS. Скопируйте маркер, щелкнув значок копирования, и сохраните его (например, в Блокноте) для дальнейшего использования.

     > [!IMPORTANT]
     > Удалите символ знака вопроса (?) в начале маркера.

     ![Область навигации][8]

4. Подключитесь к управляемому экземпляру через SQL Server Management Studio (SSMS).

5. Выполните следующую инструкцию T-SQL, чтобы **создать новые учетные данные** с использованием URL-адреса контейнера и маркера SAS, которые вы создали на предыдущих этапах.

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Выполните следующую инструкцию T-SQL, чтобы создать новый аудит сервера (выберите любое удобное имя аудита и укажите URL-адрес контейнера, созданный ранее).

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Если значение `RETENTION_DAYS` не указано, по умолчанию используется значение 0 (неограниченный период хранения).

    Дополнительные сведения см. в следующих статьях:
    - [Get started with Azure SQL Database Managed Instance Auditing](#subheading-3) (Приступая к аудиту управляемых экземпляров Базы данных SQL Azure)
    - [CREATE SERVER AUDIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Создайте спецификацию аудита сервера или спецификацию аудита базы данных, как для обычного сервера SQL Server.
    - [Руководство по созданию спецификации T-SQL для аудита сервера](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Руководство по созданию спецификации T-SQL для аудита базы данных](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Включите аудит сервера, созданный на шаге 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Анализ журналов аудита
Просмотреть журналы аудита больших двоичных объектов можно несколькими способами.

- Системная функция `sys.fn_get_audit_file` (T-SQL) возвращает данные журнала аудита в табличном формате. Дополнительные сведения об использовании этой функции см. в статье [sys.fn_get_audit_file (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Полный список методов использования журналов аудита см. в статье о [начале работы с аудитом базы данных SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Метод просмотра записей аудита на портале Azure (на панели "Записи аудита") сейчас не поддерживает управляемые экземпляры.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Различия между аудитом управляемых экземпляров, Базы данных SQL Azure и SQL Server

Ниже перечислены основные различия между аудитом SQL Управляемого экземпляра, базы данных SQL Azure и SQL Server на локальном компьютере

- Для управляемого экземпляра аудит SQL выполняется на уровне сервера, а файлы журнала `.xel` сохраняются в учетной записи хранилища BLOB-объектов Azure.
- В базе данных SQL Azure аудит SQL выполняется на уровне базы данных.
- В SQL Server на локальном компьютере или виртуальной машине аудит SQL выполняется на уровне сервера, но события сохраняются в журналы событий файловой системы или журналы событий Windows.

Аудит XEvent в Управляемом экземпляре поддерживает цели хранилища BLOB-объектов Azure. Журналы файловой системы и журналы Windows **не поддерживаются**.

Основные различия в синтаксисе `CREATE AUDIT` для аудита в хранилище BLOB-объектов Azure:
- Новый синтаксис `TO URL` позволяет указать URL-адрес контейнера в хранилище BLOB-объектов Azure, куда будут помещены файлы `.xel`.
- Синтаксис `TO FILE` **не поддерживается**, так как управляемый экземпляр не может использовать файловые ресурсы Windows.
- Параметр завершения работы **не поддерживается**.
- Значение 0 для `queue_delay` **не поддерживается**.


## <a name="next-steps"></a>Дополнительная информация

- Полный список методов использования журналов аудита см. в статье о [начале работы с аудитом базы данных SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Дополнительную информацию о программах Azure, поддерживающих проверку соблюдения стандартов, см. в [Центре управления безопасностью Azure](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
