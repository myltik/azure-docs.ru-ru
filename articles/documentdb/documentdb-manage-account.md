---
title: "Управление учетной записью Azure Cosmos DB на портале Azure | Документация Майкрософт"
description: "Узнайте, как управлять учетной записью Azure Cosmos DB на портале Azure. Изучите руководство по использованию портала Azure для просмотра, копирования, удаления учетной записи и доступа к ней."
keywords: "Портал Azure, DocumentDB, Azure, Microsoft Azure"
services: cosmosdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 59c2a0fb05809233cf789feb81189a24a18484b9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Как управлять учетной записью Azure Cosmos DB
Узнайте, как задать глобальную согласованность, как работать с ключами и как удалить учетную запись Azure Cosmos DB на портале Azure.

## <a id="consistency"></a>Управление параметрами согласованности Azure Cosmos DB
Выбор правильного уровня согласованности зависит от семантики вашего приложения. Сведения о доступных настраиваемых уровнях согласованности данных в Azure Cosmos DB см. в [этой статье][consistency]. Azure Cosmos DB предоставляет гарантии согласованности, доступности и производительности для любого уровня согласованности, доступного учетной записи базы данных. Чтобы настроить для учетной записи базы данных строгий уровень согласованности, требуется, чтобы данные находились только в одном регионе Azure и не были глобально доступны. С другой стороны, нестрогие уровни согласованности (с ограниченным устареванием, уровня сеанса или согласованность в конечном счете) дают возможность связать любое количество регионов Azure с учетной записью базы данных. Следующие простые действия показывают, как выбрать уровень согласованности по умолчанию для учетной записи базы данных. 

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Установка согласованности по умолчанию для учетной записи Azure Cosmos DB
1. Войдите в свою учетную запись Azure Cosmos DB на [портале Azure](https://portal.azure.com/).
2. В колонке учетной записи щелкните **Согласованность по умолчанию**.
3. В колонке **Согласованность по умолчанию** выберите новый уровень согласованности и щелкните **Сохранить**.
    ![Сеанс согласованности по умолчанию][5]

## <a id="keys"></a>Просмотр, копирование и повторное создание ключей доступа
При создании учетной записи Azure Cosmos DB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи Azure Cosmos DB. Предоставляя два ключа, Azure Cosmos DB позволяет вам повторно создавать ключи без перерывов в работе учетной записи Azure Cosmos DB. 

На [портале Azure](https://portal.azure.com/) откройте колонку **Ключи** из меню ресурсов в колонке **учетной записи Azure Cosmos DB**, чтобы получить возможность просмотра, копирования и повторного создания ключей доступа, используемых для доступа к вашей учетной записи Azure Cosmos DB.

![Снимок экрана портала Azure, колонка «Ключи»](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> Колонка **Ключи** также содержит первичную и вторичную строки подключения, используемые для подключения к учетной записи из [средства переноса данных](documentdb-import-data.md).
> 
> 

В этой колонке также доступны ключи только для чтения. Чтение и запросы являются операциями только для чтения, а создание, удаление и замена — нет.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Копирование ключа доступа на портале Azure
В колонке **Ключи** нажмите кнопку **Копировать** справа от того ключа, который требуется скопировать.

![Просмотр и копирование ключа доступа на портале Azure, колонка «Ключи»](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Повторное создание ключей доступа
Вам следует периодически менять ключи доступа для своей учетной записи Azure Cosmos DB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи Azure Cosmos DB с помощью одного ключа, одновременно повторно создавая второй ключ.

> [!WARNING]
> Повторное создание ключей доступа влияет на все приложения, которые зависят от текущего ключа. Необходимо обновить все клиенты, использующие ключ доступа для обращения к учетной записи Azure Cosmos DB, чтобы они использовали новый ключ.
> 
> 

Если имеются приложения или облачные службы, использующие учетную запись Azure Cosmos DB, вы потеряете эти подключения при повторном создании ключей, пока не возобновите ключи. Ниже описан процесс возобновления ключей.

1. Обновите ключ доступа в коде приложения, чтобы задать ссылку на дополнительный ключ доступа учетной записи Azure Cosmos DB.
2. Повторно создайте главный ключ доступа для своей учетной записи Azure Cosmos DB. Войдите в свою учетную запись Azure Cosmos DB на [портале Azure](https://portal.azure.com/).
3. В колонке **учетной записи Azure Cosmos DB** щелкните **Ключи**.
4. В колонке **Ключи** нажмите кнопку "Повторно создать", а затем кнопку **ОК**, чтобы подтвердить создание ключа.
    ![Повторное создание ключей доступа](./media/documentdb-manage-account/regenerate-keys.png)
5. Убедившись, что новый ключ доступен для использования (примерно через 5 минут после повторного создания), обновите ключ доступа в коде приложения, чтобы задать ссылку на новый главный ключ доступа.
6. Повторно создайте дополнительный ключ доступа.
   
    ![Повторное создание ключей доступа](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Для использования нового ключа для доступа к учетной записи Azure Cosmos DB может потребоваться подождать несколько минут.
> 
> 

## <a name="get-the--connection-string"></a>Получение строки подключения
Чтобы получить строку подключения, выполните следующее. 

1. Войдите в свою учетную запись Azure Cosmos DB на [портале Azure](https://portal.azure.com).
2. В меню ресурсов выберите **Ключи**.
3. Нажмите кнопку **Копировать** рядом с полем **Первичная строка подключения** или **Вторичная строка подключения**. 

Если вы используете строку подключения в [средстве миграции базы данных Azure Cosmos DB](documentdb-import-data.md), добавьте имя базы данных в конец строки подключения. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a>Удаление учетной записи Azure Cosmos DB
Чтобы удалить учетную запись Azure Cosmos DB, которая больше не нужна, щелкните правой кнопкой мыши имя учетной записи и выберите команду **Удалить учетную запись**.

![Как удалить учетную запись Azure Cosmos DB на портале Azure](./media/documentdb-manage-account/deleteaccount.png)

1. На [портале Azure](https://portal.azure.com/) перейдите в учетную запись Azure Cosmos DB, которую необходимо удалить.
2. В колонке **учетной записи Azure Cosmos DB** щелкните правой кнопкой мыши учетную запись и выберите команду **Удалить учетную запись**. 
3. В появившейся колонке подтверждения введите имя учетной записи Azure Cosmos DB, чтобы подтвердить ее удаление.
4. Нажмите кнопку **Удалить** .

![Как удалить учетную запись Azure Cosmos DB на портале Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a id="next"></a>Дальнейшие действия
Узнайте о том, как [приступить к работе с учетной записью Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

