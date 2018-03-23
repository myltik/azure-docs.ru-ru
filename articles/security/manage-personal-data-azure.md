---
title: Управление персональными данными в Microsoft Azure | Документация Майкрософт
description: Исправление, обновление, удаление и экспорт персональных данных для соблюдения Общего регламента по защите данных (GDPR) в Azure Active Directory и Базе данных SQL Azure.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: 41c0cc4eb3697aa79abeabddc98a84598ce4ea50
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Управление персональными данными в Microsoft Azure

Эта статья содержит указания по исправлению, обновлению, удалению и экспорту персональных данных для соблюдения Общего регламента по защите данных (GDPR) в Azure Active Directory и Базе данных SQL Azure.

## <a name="scenario"></a>Сценарий

Дублинская компания предоставляет свадебные товары и услуги в Ирландии и по всему миру. Благодаря офисам, клиентам, сотрудникам и поставщикам в разных странах она может полностью обслуживать мероприятия, которые предлагает.

Кроме того, сотрудники компании отслеживают соблюдение условий нестандартного качества обслуживания, таких, например, как учет пищевой аллергии и диетических предпочтений. Накануне события на центральной веб-странице гости свадьбы могут зарегистрироваться на различные мероприятия, такие как катание на лошадях, серфинг, катание на лодках и т. д., или пообщаться друг с другом. Компания собирает персональные данные сотрудников, поставщиков, клиентов и гостей свадьбы. Так как компания предоставляет свои услуги по всему миру, она должна соответствовать нескольким уровням нормативного регулирования.

## <a name="problem-statement"></a>Проблема

- Администраторы данных должны иметь возможность исправлять неточные персональные данные и обновлять неполные или изменившиеся.

- Администраторы данных должны иметь возможность удалять персональные данные по запросу их владельца.

- Администраторы данных должны экспортировать и предоставлять данные по запросу их владельца в распространенном, структурированном формате.

## <a name="company-goals"></a>Цели компании

- Компания должна реализовать возможность исправлять или обновлять неточные или неполные персональные данные клиентов, гостей свадьбы, сотрудников и поставщиков в Azure Active Directory и базе данных SQL Azure.

- Персональные данные должны удаляться из Azure Active Directory и базы данных SQL Azure по запросу их владельца.

- Личные данные должны экспортироваться в распространенном, структурированном формате по запросу их владельца.

## <a name="solutions"></a>Решения

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Изменение неверных или неполных персональных данных и удаление персональных данных и профилей пользователей в Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) — это мультитенантный облачный каталог и служба управления удостоверениями Майкрософт.
Вы можете исправить, обновить или удалить пользовательские профили клиентов и сотрудников и сведения о работе пользователей, содержащие персональные данные, такие как имя пользователя, профессия, адрес или номер телефона, в [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) с помощью [портала Azure](https://portal.azure.com/).

Вы должны выполнить вход с помощью учетной записи глобального администратора каталога.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Исправление или обновление профиля пользователя и сведений о работе в Azure Active Directory

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2. Выберите **Все службы**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. В колонке **Пользователи и группы** выберите **Пользователи**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. В колонке **Пользователи и группы — Пользователи** выберите пользователя в списке, а затем в открывшейся колонке щелкните **Профиль**, чтобы просмотреть соответствующие сведения, которые нужно исправить или обновить.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Исправьте или обновите сведения, а затем на панели команд выберите **Сохранить**.

6.  В колонке выбранного пользователя выберите **Сведения о работе**, чтобы просмотреть сведения о его работе, которые необходимо исправить или обновить.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Исправьте или обновите сведения о работе пользователя, а затем на панели команд выберите **Сохранить**.

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Удаление профиля пользователя в Azure Active Directory

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2. Выберите **Все службы**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

    ![](media/manage-personal-data-azure/image001.png)

3. В колонке **Пользователи и группы** выберите **Пользователи**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. В колонке **Пользователи и группы — Пользователи** выберите пользователя из списка.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. В колонке для выбранного пользователя щелкните **Обзор**, а затем на панели команд щелкните **Удалить**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>Удаление, экспорт и изменение неверных или неполных персональных данных в базе данных SQL 

[База данных SQL Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) — это облачная база данных, позволяющая разработчикам создавать приложения и управлять ими.

Персональные данные в [базе данных SQL Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) можно обновить с помощью стандартных запросов SQL, а также удалить. Кроме того, персональные данные можно экспортировать из базы данных SQL с помощью различных методов, например с помощью мастера импорта и экспорта Azure SQL Server, и в различных форматах, в том числе как файл BACPAC.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Исправление, обновление и удаление персональных данных в базе данных SQL

Сведения об исправлении и обновлении персональных данных в базе данных SQL см. в статьях [UPDATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [UPDATETEXT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [WITH common_table_expression (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) и [WRITETEXT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql).

Сведения об удалении персональных данных в базе данных SQL см. в статье [DELETE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Экспорт персональных данных в файл BACPAC в базе данных SQL

Файл BACPAC включает данные и метаданные базы данных SQL. Это ZIP-файл с расширением BACPAC. Персональные данные можно экспортировать с помощью [портала Azure](https://portal.azure.com/), служебной программы командной строки SQLPackage, SQL Server Management Studio (SSMS) или PowerShell.

Сведения об экспорте данных в файл BACPAC и соответствующие инструкции для каждого из перечисленных выше методов см. в статье [Экспорт базы данных SQL Azure в BACPAC-файл](https://docs.microsoft.com/azure/sql-database/sql-database-export).

Экспорт персональных данных из базы данных SQL с помощью мастера экспорта и импорта SQL Server

Этот мастер помогает скопировать данные из источника в место назначения. Дополнительные сведения о мастере, в том числе сведения о его получении, разрешениях и получении помощи при работе с ним, см. на веб-странице [Импорт и экспорт данных с помощью мастера импорта и экспорта SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard).

Дополнительные сведения о шагах в мастере см. на веб-странице [Steps in the SQL Server Import and Export Wizard](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) (Шаги в мастере импорта и экспорта SQL Server).

## <a name="next-steps"></a>Дальнейшие действия

[база данных SQL Azure;](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

