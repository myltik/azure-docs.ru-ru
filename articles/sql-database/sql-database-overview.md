---
title: "Общие сведения о базе данных SQL Azure | Документация Майкрософт"
description: "Здесь содержатся общие сведения о базах данных SQL Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 6d5f4640556f98f9601139c318ffc578e9df2539


---
# <a name="azure-sql-database-overview"></a>Общие сведения о базе данных SQL Azure
В этой статье приведены общие сведения о базах данных SQL Azure. Дополнительные сведения о логических серверах SQL Azure см. в [этой статье](sql-database-server-overview.md).

## <a name="what-is-azure-sql-database"></a>Сведения о базе данных SQL Azure
Каждая база данных в базе данных SQL Azure связана с логическим сервером. База данных может:

- представлять собой автономную базу данных с [собственным набором ресурсов](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU);
- входить в состав [эластичного пула](sql-database-elastic-pool.md), в котором [набор ресурсов используется совместно](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU);
- входить в состав [масштабируемого набора сегментированных баз данных](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), которые могут быть отдельными базами данных или частью пула;
- входить в состав набора баз данных, использующихся в [шаблоне разработки для мультитенантных приложений SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md), которые могут быть отдельными базами данных или частью пула (или этот набор может иметь смешанную конфигурацию). 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Подключение к базе данных SQL Azure и выполнение проверки подлинности

- **Проверка подлинности и авторизация.** База данных SQL Azure поддерживает проверку подлинности SQL и Azure Active Directory (сведения об ограничениях, касающихся проверки подлинности, см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md)). Вы можете подключиться к базе данных SQL Azure и выполнить проверку подлинности через главную базу данных сервера или непосредственно с помощью пользовательской базы данных. Дополнительные сведения см. в статье [Управление базами данных и учетными записями в базе данных SQL Azure](sql-database-manage-logins.md). Проверка подлинности Windows не поддерживается. 
- **TDS.** База данных SQL Microsoft Azure поддерживает клиент протокола для потока табличных данных (TDS) версии 7.3 или более поздней.
- **TCP/IP.** Разрешены только подключения по протоколу TCP/IP.
- **Брандмауэр базы данных SQL.** Для защиты данных брандмауэр базы данных SQL запрещает любой доступ к серверу базы данных или базам данных, пока не будут указаны компьютеры, которые имеют разрешение. Дополнительные сведения см. в статье о [брандмауэрах](sql-database-firewall-configure.md).

## <a name="what-collations-are-supported"></a>Поддерживаемые параметры сортировки
Параметры сортировки базы данных по умолчанию, используемые базой данных SQL Microsoft Azure, — это **SQL_LATIN1_GENERAL_CP1_CI_AS**, где **LATIN1_GENERAL** — английский язык (США), **CP1** — кодовая страница 1252, **CI** выполняется без учета регистра, а **AS** означает, что учитываются диакритические знаки. Нельзя изменить параметры сортировки для баз данных версии 12. Дополнительные сведения о настройке параметров сортировки см. в статье [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Требования к именованию объектов базы данных

Имена всех новых объектов должны соответствовать правилам SQL Server, действующим в отношении идентификаторов. Дополнительные сведения см. в статье [Идентификаторы баз данных](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Функции, поддерживаемые в базах данных SQL Azure

Дополнительные сведения о поддерживаемых функциях см. [здесь](sql-database-features.md). Дополнительные сведения о причинах отсутствия поддержки некоторых функций см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).

## <a name="how-do-i-manage-an-azure-sql-database"></a>Управление базой данных SQL Azure

Логическими серверами базы данных SQL Azure можно управлять с помощью нескольких средств:
- [Портал Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о службе базы данных SQL Azure см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Дополнительные сведения о поддерживаемых функциях см. [здесь](sql-database-features.md).
- Общие сведения о логических серверах SQL Azure см. в [этой статье](sql-database-server-overview.md).
- Сведения о поддержке функций Transact-SQL в базе данных SQL Azure см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).
- Ознакомьтесь со сведениями о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб**. Общие сведения об уровнях служб см. в статье [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md).
- Рекомендации по безопасности см. в статье [Безопасность в базе данных SQL Azure: рекомендации и ограничения](sql-database-security-guidelines.md).
- Сведения о доступности драйверов и поддержке для базы данных SQL см. в статье [Библиотеки подключений для базы данных SQL и SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO2-->


