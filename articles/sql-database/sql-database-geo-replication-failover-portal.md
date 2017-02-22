---
title: "Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure | Документация Майкрософт"
description: "Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a9d184a4-09e0-4f41-b364-40425f68f430
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: b0180a9f32e1176667fe8e33a4151b2b70956adc


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure

В этой статье объясняется, как запустить отработку отказа в базе данных-получателе SQL Azure с помощью [портала Azure](http://portal.azure.com). Сведения о настройке георепликации для баз данных SQL Azure см. в [этой статье](sql-database-geo-replication-portal.md).

## <a name="initiate-a-failover"></a>Запуск отработки отказа
Базу данных-получатель можно сделать источником.  

1. На [портале Azure](http://portal.azure.com) перейдите к базе данных-источнику в партнерстве георепликации.
2. В колонке "База данных SQL" выберите **Все параметры** > **Георепликация**.
3. В списке **Получатели** выберите базу данных, которая должна стать новым источником, и щелкните **Отработка отказа**.
   
    ![Отработка отказа][2]
4. Выберите **Да** , чтобы запустить отработку отказа.

Команда незамедлительно переведет базу данных-получателя в основную роль. 

В течение короткого периода (от 0 до 25 секунд), пока переключаются роли, обе базы данных будут недоступны. Если база данных-источник имеет несколько баз данных-получателей, команда автоматически перенастроится на другие базы данных-получатели для подключения к новой базе данных-источнику. Обычно вся операция занимает меньше минуты. 

> [!NOTE]
> Если при отправке команды источник находится в сети и выполняет транзакции, некоторые данные могут быть утеряны.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
* После отработки отказа убедитесь, что для новой базы данных-источника настроены требования аутентификации ваших сервера и базы данных. Дополнительные сведения см. в разделе [Безопасность базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md).
* Чтобы изучить восстановление после сбоя с помощью активной георепликации, включая предварительные и последующие действия и отработку аварийного восстановления, ознакомьтесь с разделом [Отработка аварийного восстановления](sql-database-disaster-recovery.md)
* Прочитайте запись блога Александра Носова об активной георепликации: [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Сведения о проектировании облачных приложений для использования активной георепликации см. в статье [Создание приложения для аварийного восстановления облака с использованием активной георепликации в базе данных SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).
* Сведения об использовании активной георепликации с пулами эластичных баз данных см. в статье [Стратегии аварийного восстановления для приложений с использованием пула эластичных баз данных SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Общие сведения об обеспечении непрерывности бизнес-процессов можно узнать в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)

<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png



<!--HONumber=Feb17_HO3-->


