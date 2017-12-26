---
title: "Создание запроса в службу поддержки для хранилища данных SQL | Документация Майкрософт"
description: "Порядок создания запроса в службу поддержки для хранилища данных SQL Azure"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 66aa12431e39ce295ce76411693bd20535a5c70c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Создание запроса в службу поддержки для хранилища данных SQL
Если у вас возникли проблемы с хранилищем данных SQL, создайте запрос в службу поддержки, чтобы получить помощь от нашей службы поддержки разработчиков.

## <a name="create-a-support-ticket"></a>Создание запроса в службу поддержки
1. Откройте [портал Azure][Azure portal].
2. На начальном экране выберите вкладку **Справка и поддержка**.
   
    ![Справка + поддержка](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. В колонке "Справка и поддержка" щелкните **Новый запрос на поддержку** и заполните колонку **Основные сведения**.

   Выберите свой [план поддержки Azure][Azure support plan].
   
   * Поддержка по **вопросам, связанным с выставлением счетов, квотами и управлением подпиской**, доступна на всех уровнях.
   * Поддержка по **замене или ремонту** обеспечивается на уровнях [Разработка][Developer], [Стандартный][Standard], [Professional Direct][Professional Direct] и [Premier][Premier]. Проблемы характера «замена или ремонт» — это возникающие при использовании Azure проблемы, относительно которых имеются обоснованные предположения, что их причиной стала корпорация Майкрософт.
   * Услуги **обучения** и **консультирования разработчиков** доступны на уровнях поддержки [Professional Direct][Professional Direct] и [Premier][Premier]. 
     
     При наличии плана поддержки Premier можно также создавать отчеты по проблемам с хранилищем данных SQL на [портале Microsoft Premier][Microsoft Premier online portal].  Дополнительные сведения о доступной поддержке, включая области действия, время ответа, цены и т. д., см. на странице [планов поддержки Azure][Azure support plan].  Часто задаваемые вопросы о поддержке Azure см. на [этой странице][Azure support FAQs].  
        
    ![Колонка "Основные сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
    ![колонка1 "Основные сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Заполните колонку **Проблема**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > По умолчанию для каждого экземпляра SQL Server (например, myserver.database.windows.net) предусмотрена следующая **квота на DTU**: 45 000. Эта квота является просто ограничением для безопасности. Вы можете увеличить квоту, отправив запрос в службу поддержки и указав *Квота* в качестве типа запроса. Чтобы вычислить потребности в DTU, умножьте 7,5 на общее необходимое количество [DWU][DWU]. Например, вы хотите разместить две базы данных DW6000 на одном сервере SQL Server. Следует запросить квоту DTU 90 000.  Текущее потребление DTU можно просмотреть в колонке SQL Server на портале. В рамках квоты DTU учитываются как приостановленные, так и активные базы данных. 
   > 
   > 
   
5. Введите **контактные данные**.
![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Щелкните **Создать** , чтобы отправить запрос в службу поддержки.

## <a name="monitor-a-support-ticket"></a>Мониторинг запроса в службу поддержки
После отправки запроса с вами свяжется представитель службы поддержки Azure. Чтобы проверить состояние запроса и сведения о нем, щелкните **Все запросы на поддержку** на панели мониторинга.

![Проверка состояния](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Другие ресурсы:
Кроме того, вы можете присоединиться к сообществу пользователей хранилища данных SQL на странице [Stack Overflow][Stack Overflow] или обратиться на [форум MSDN, посвященный хранилищу данных SQL Azure][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

