---
title: Включение прозрачного шифрования данных для Stretch Database с помощью T-SQL в Azure | Документация Майкрософт
description: Включение прозрачного шифрования данных (TDE) для SQL Server Stretch Database в Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: jhubbard
editor: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: anvang
ms.openlocfilehash: ed26c2b386e08b76f78b4a05e12c46d2b97c20f2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23055459"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Включение прозрачного шифрования данных (TDE) для Stretch Database в Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [портал Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Прозрачное шифрование данных (TDE) помогает защититься от угрозы вредоносных атак за счет шифрования и расшифровки базы данных, связанных резервных копий и файлов журналов транзакций при хранении в реальном времени, не внося изменения в само приложение.

При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. Ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Azure обладает уникальным встроенным сертификатом. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней. Общие сведения о прозрачном шифровании данных см. в [этой статье].

## <a name="enabling-encryption"></a>Включение шифрования
Чтобы включить прозрачное шифрование для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Подключитесь к базе данных *master* на сервере Azure, где находится база данных, указав имя входа администратора или члена роли **dbmanager** в базе данных master.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Отключение шифрования
Чтобы отключить прозрачное шифрование для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Подключитесь к *главной* базе данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Проверка шифрования
Чтобы проверить состояние шифрования для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Подключитесь к *главной* базе данных или к экземпляру базы данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Результат ```1``` означает зашифрованную, а ```0``` — незашифрованную базу данных.

<!--Anchors-->
[этой статье]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
