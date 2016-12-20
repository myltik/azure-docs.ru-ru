---
title: "Перенос баз данных между серверами, между подписками, в службу Azure и из нее"
description: "Быстрый порядок копирования, перемещения и переноса данных и баз данных в базу данных SQL Azure."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Перенос баз данных между серверами, между подписками, в среду Azure и из нее
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Перенос базы данных на другой сервер в пределах одной подписки
* На [портале Azure](https://portal.azure.com) щелкните **Базы данных SQL**, выберите базу данных из списка и щелкните **Копировать**. Дополнительные сведения см. в статье [Копирование базы данных SQL Azure](sql-database-copy.md).

## <a name="to-move-a-database-between-subscriptions"></a>Перенос базы данных из одной подписки в другую
* На [портале Azure](https://portal.azure.com)щелкните **Серверы SQL** и выберите в списке сервер, на котором размещена ваша база данных. Щелкните **Перенести**и выберите ресурсы, которые нужно перенести, а также подписку, в которую их нужно переместить.

## <a name="to-migrate-a-sql-database-into-azure"></a>Перенос базы данных SQL в Azure
* Определите совместимость баз данных и выберите подходящий способ переноса. Выполните инструкции и настройте параметры [мастера переноса базы данных SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Создание копии базы данных для использования за пределами Azure
* [Экспортируйте файл BACPAC.](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


