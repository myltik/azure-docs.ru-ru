---
title: "Управление учетной записью DocumentDB на портале Azure | Документация Майкрософт"
description: "Узнайте, как управлять учетной записью DocumentDB на портале Azure. Изучите руководство по использованию портала Azure для просмотра, копирования, удаления учетной записи и доступа к ней."
keywords: "Портал Azure, DocumentDB, Azure, Microsoft Azure"
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f38b5d6127897ba2b083e0f3bb41562650214ae9


---
# <a name="how-to-manage-a-documentdb-account"></a>Управление учетной записью DocumentDB
Узнайте, как задать глобальную согласованность, как работать с ключами и как удалить учетную запись DocumentDB на портале Azure.

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>Управление параметрами согласованности DocumentDB
Выбор правильного уровня согласованности зависит от семантики вашего приложения. Следует ознакомиться с доступными уровнями согласованности в DocumentDB: [Использование уровней согласованности для обеспечения максимальной доступности и производительности в DocumentDB][consistency]. DocumentDB предоставляет гарантии согласованности, доступности и производительности для любого уровня согласованности, доступного учетной записи базы данных. Чтобы настроить для учетной записи базы данных строгий уровень согласованности, требуется, чтобы данные находились только в одном регионе Azure и не были глобально доступны. С другой стороны, нестрогие уровни согласованности (с ограниченным устареванием, уровня сеанса или согласованность в конечном счете) дают возможность связать любое количество регионов Azure с учетной записью базы данных. Следующие простые действия показывают, как выбрать уровень согласованности по умолчанию для учетной записи базы данных. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Установка согласованности по умолчанию для учетной записи DocumentDB
1. На [портале Azure](https://portal.azure.com/)перейдите к своей учетной записи DocumentDB.
2. В колонке учетной записи щелкните **Согласованность по умолчанию**.
3. В колонке **Согласованность по умолчанию** выберите новый уровень согласованности и щелкните **Сохранить**.
    ![Сеанс согласованности по умолчанию][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>Просмотр, копирование и повторное создание ключей доступа
При создании учетной записи DocumentDB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи DocumentDB. Предоставляя два ключа, DocumentDB позволяет вам повторно создавать ключи без перерывов в работе учетной записи DocumentDB. 

На [портале Azure](https://portal.azure.com/) откройте колонку **Ключи** из меню ресурсов в колонке **Учетная запись DocumentDB**, чтобы получить возможность просмотра, копирования и повторного создания ключей доступа, используемых для доступа к вашей учетной записи DocumentDB.

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
Вам следует периодически менять ключи доступа для своей учетной записи DocumentDB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи DocumentDB с помощью одного ключа, одновременно повторно создавая второй ключ.

> [!WARNING]
> Повторное создание ключей доступа влияет на все приложения, которые зависят от текущего ключа. Необходимо обновить все клиенты, использующие ключ доступа для обращения к учетной записи DocumentDB, чтобы они использовали новый ключ.
> 
> 

Если имеются приложения или облачные службы, использующие учетную запись DocumentDB, вы потеряете эти подключения при повторном создании ключей, пока не возобновите ключи. Ниже описан процесс возобновления ключей.

1. Обновите ключ доступа в коде приложения, чтобы задать ссылку на дополнительный ключ доступа учетной записи DocumentDB.
2. Повторно создайте главный ключ доступа для своей учетной записи DocumentDB. На [портале Azure](https://portal.azure.com/)перейдите к своей учетной записи DocumentDB.
3. В колонке **Учетная запись DocumentDB** щелкните **Ключи**.
4. В колонке **Ключи** нажмите кнопку "Повторно создать", а затем кнопку **ОК**, чтобы подтвердить создание ключа.
    ![Повторное создание ключей доступа](./media/documentdb-manage-account/regenerate-keys.png)
5. Убедившись, что новый ключ доступен для использования (примерно через 5 минут после повторного создания), обновите ключ доступа в коде приложения, чтобы задать ссылку на новый главный ключ доступа.
6. Повторно создайте дополнительный ключ доступа.
   
    ![Повторное создание ключей доступа](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Для использования нового ключа для доступа к учетной записи DocumentDB может потребоваться подождать несколько минут.
> 
> 

## <a name="get-the-connection-string"></a>Получение строки подключения
Чтобы получить строку подключения, выполните следующее. 

1. На [портале Azure](https://portal.azure.com) перейдите к своей учетной записи DocumentDB.
2. В меню ресурсов выберите **Ключи**.
3. Нажмите кнопку **Копировать** рядом с полем **Первичная строка подключения** или **Вторичная строка подключения**. 

Если вы используете строку подключения в инструменте [DocumentDB Database Migration Tool](documentdb-import-data.md), добавьте имя базы данных в конец строки подключения. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> Удаление учетной записи DocumentDB
Чтобы удалить учетную запись DocumentDB, которая больше не нужна, с портала Azure, воспользуйтесь командой **Удалить учетную запись** в колонке **Учетная запись DocumentDB**.

![Как удалить учетную запись DocumentDB на портале Azure](./media/documentdb-manage-account/deleteaccount.png)

1. На [портале Azure](https://portal.azure.com/)перейдите в учетную запись DocumentDB, которую необходимо удалить.
2. В колонке **Учетная запись DocumentDB** щелкните **Дополнительно**, а затем выберите **Удалить учетную запись**. Или щелкните правой кнопкой мыши имя базы данных и выберите команду **Удалить учетную запись**.
3. В появившейся колонке подтверждения введите имя учетной записи DocumentDB, чтобы подтвердить ее удаление.
4. Нажмите кнопку **Удалить** .

![Как удалить учетную запись DocumentDB на портале Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>Дальнейшие действия
Узнайте о том, как [приступить к работе с учетной записью DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Дополнительные сведения о DocumentDB можно найти в документации по Azure DocumentDB на сайте [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

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
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO3-->


