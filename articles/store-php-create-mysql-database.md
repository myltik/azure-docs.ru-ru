---
title: "Создание базы данных MySQL и подключение к ней в Azure"
description: "Из этой статьи вы узнаете, как с помощью портала Azure создать базу данных MySQL и подключиться к ней из веб-приложения PHP в Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Создание базы данных MySQL и подключение к ней в Azure
В этом учебнике объясняется, как создать базу данных MySQL на [портале Azure](https://portal.azure.com) (поставщик — [ClearDB](http://www.cleardb.com/)) и подключиться к ней из веб-приложения PHP, запущенного в [службе приложений Azure](app-service/app-service-web-overview.md).

> [!NOTE]
> Кроме того, вы можете создать базу данных MySQL как часть <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">шаблона приложения Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Создание базы данных MySQL на портале Azure
Чтобы создать базу данных MySQL на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева выберите пункт **Создать** > **Данные + хранилище** > **База данных MySQL**.

    ![Создание базы данных MySQL на портале Azure — начало](./media/store-php-create-mysql-database/create-db-1-start.png)
3. В [колонке](azure-portal-overview.md)(*колонка*— это страница портала, которая открывается горизонтально) новой базы данных MySQL настройте базу данных.

   * **Имя базы данных**: введите уникальное имя.
   * **Подписка**: выберите подписку, которую нужно использовать.
   * **Тип базы данных**: выберите **Общий** для недорогих или бесплатных категорий или **Выделенный** для получения выделенных ресурсов.
   * **Группа ресурсов**: добавьте базу данных MySQL в существующую или новую [группу ресурсов](azure-resource-manager/resource-group-overview.md) . Ресурсами, которые находятся в одной группе, легко управлять.
   * **Расположение**: выберите близкое к вам расположение. При добавлении элементов в существующую группу ресурсов вам автоматически назначается расположение этой группы.
   * **Ценовая категория**: щелкните **Ценовая категория**, затем выберите один из вариантов (категория **Меркурий** бесплатная) и щелкните **Выбрать**.
   * **Юридические условия**: щелкните **Юридические условия**, просмотрите сведения о покупке и нажмите кнопку **Приобрести**.
   * **Закрепление на панели мониторинга**: укажите, хотите ли вы получить к элементу доступ непосредственно на панели мониторинга. Это особенно удобно, если вы еще не знакомы с навигацией по порталу.

     На следующем снимке экрана представлен пример того, как вы можете настроить базу данных MySQL.  
     ![Создание базы данных MySQL на портале Azure — настройка](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Настроив все параметры, щелкните **Создать**.

    ![Создание базы данных MySQL на портале Azure — создание](./media/store-php-create-mysql-database/create-db-3-create.png)

    Отобразится всплывающее уведомление о запуске развертывания.

    ![Создание базы данных MySQL на портале Azure — выполнение](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    По завершении развертывания отобразится еще одно всплывающее окно. Кроме того, на портале также автоматически откроется колонка базы данных MySQL.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Подключение к базе данных MySQL
Чтобы отобразить сведения о подключении для новой базы данных MySQL, просто щелкните **Свойства** в колонке своего веб-приложения.

![Создание базы данных MySQL на портале Azure — колонка базы данных MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Теперь эти сведения о подключении вы можете использовать в любом веб-приложении. [Здесь](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)доступен пример того, как использовать сведения о подключении в простом приложении PHP.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию можно найти в [Центре разработчика PHP](/develop/php/).
