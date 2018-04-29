---
title: Часто задаваемые вопросы о службе Azure Database Migration Service | Документация Майкрософт
description: Ознакомьтесь с часто задаваемыми вопросами об использовании службы Azure Database Migration Service для переноса базы данных.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 85052e1363ebbfe21cd7d6d5b3720f79cec7c417
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Часто задаваемые вопросы о службе Azure Database Migration Service
В этой статье приведены часто задаваемые вопросы об использовании службы Azure Database Migration Service вместе с соответствующими ответами.

### <a name="q-what-is-azure-database-migration-service"></a>В. Что такое служба Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя. Служба, которая сейчас доступна в режиме общедоступной предварительной версии, оптимизируется для включения следующих возможностей:
- надежность и производительность;
- итеративное добавление пар исходного и целевого объектов;
- работа над функциями непрерывной миграции.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>В. Какие пары исходного и целевого объектов в настоящее время поддерживает служба Azure Database Migration Service?
Служба находится в режиме общедоступной предварительной версии и в настоящее время поддерживает перенос из SQL Server в базу данных SQL Azure. Теперь вы можете перейти на портал Azure, чтобы начать использовать службу Azure Database Migration Service для этого сценария. Другие пары исходного и целевого объектов, такие как SQL Server и управляемый экземпляр базы данных SQL Azure, а также Oracle и база данных SQL Azure, доступны в ограниченной закрытой предварительной версии. Чтобы принять участие в ограниченной закрытой предварительной версии этих сценариев, зарегистрируйтесь [здесь](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>В. Как служба Azure Database Migration Service соотносится с другими инструментами переноса базы данных Майкрософт, такими как Data Migration Assistant (DMA) или Помощник по миграции SQL Server (SSMA)?
Служба Azure Database Migration Service является предпочтительным методом переноса базы данных в Microsoft Azure в нужном масштабе. Дополнительные сведения о том, как служба Azure Database Migration Service соотносится с другими инструментами переноса базы данных Майкрософт, и рекомендации по использованию службы в различных сценариях см. в записи блога [Differentiating Microsoft’s Database Migration Tools and Services](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/) (Различия инструментов и служб по переносу базы данных Майкрософт).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>В. Как служба Azure Database Migration Service соотносится с предложением "Миграция Azure"?
Служба "Миграция Azure" помогает выполнять миграцию локальных виртуальных машин на виртуальные машины IaaS Azure. Оценивается пригодность для миграции, показатели производительности, на основе которых определяется требуемый размер, и расходы на работу локальных виртуальных машин в Azure. Служба "Миграция Azure" подходит для переноса рабочих нагрузок на локальных виртуальных машинах на виртуальные машины IaaS Azure методом lift-and-shift. Однако, в отличие от службы Azure Database Migration Service, служба "Миграция Azure" не является специализированным предложением службы переноса базы данных для платформ реляционных баз данных PaaS Azure, таких как база данных SQL Azure или управляемый экземпляр базы данных SQL Azure.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>В. Какие действия необходимо выполнить, чтобы использовать службу Azure Database Migration Service для переноса базы данных?
Во время обычного переноса базы данных вы:
1.  Создаете целевую базу данных.
2.  Переносите схему базы данных с помощью [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  создание экземпляра Azure Database Migration Service;
4.  Создаете проект миграции, указав исходную базу данных, целевую базу данных и таблицы для переноса.
5.  Инициируете полную загрузку.
6.  Выбираете последующую проверку.
7.  Вручную переключаете рабочую среду на использование новой облачной базы данных. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>В. Каковы предварительные требования для использования службы Azure Database Migration Service?
Есть несколько предварительных требований, которые необходимо выполнить, чтобы служба Azure Database Migration Service работала без проблем при переносе базы данных. Некоторые предварительные требования применяются во всех сценариях (пар исходного и целевого объектов), поддерживаемых службой, тогда как другие уникальны и используются в определенных сценариях.
Ниже приведены предварительные требования для использования службы Azure Database Migration Service, которые применяются во всех поддерживаемых сценариях миграции.
- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure не блокируют порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
 
Список всех предварительных требований, которые нужно выполнить для реализации определенного сценария миграции с использованием службы Azure Database Migration Service, см. в соответствующих руководствах на странице [документации](https://docs.microsoft.com/azure/dms/dms-overview) на сайте docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>В. Как найти IP-адрес для службы Azure Database Migration Service, чтобы внести его в список разрешений в рамках правил брандмауэра, контролирующих доступ к исходной базе данных для переноса?
Вам может потребоваться добавить правила брандмауэра, позволяющие службе Azure Database Migration Service получать доступ к исходной базе данных для выполнения переноса. IP-адрес для этой службы является динамическим, однако при использовании Express Route этот адрес будет в закрытом порядке назначен корпоративной сети. Самый простой способ определить соответствующий IP-адрес — перейти в ту же группу ресурсов, в которой подготовлен ресурс службы Azure Database Migration Service, чтобы найти связанный сетевой интерфейс. Обычно имя ресурса сетевого интерфейса начинается с префикса NIC, за которым следует уникальная последовательность знаков и символов, например NIC-jj6tnztnmarpsskr82rbndyp. Когда вы выберете этот ресурс, отобразится IP-адрес, который необходимо добавить в список разрешений на странице обзора ресурсов на портале Azure.

Вам также может потребоваться добавить в список разрешений исходный порт, с которого SQL Server ожидает передачи данных. По умолчанию — это порт 1433, однако исходный SQL Server также можно настроить на ожидание передачи данных с других портов. В этом случае необходимо добавить эти порты в список разрешений. Определить порт, который SQL Server прослушивает, можно с помощью запроса динамического административного представления:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Вы также можете определить порт, который SQL Server прослушивает, запросив журнал ошибок SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>В. Есть ли рекомендации по оптимизации производительности службы Azure Database Migration Service?
Вы можете выполнить ряд действий, чтобы ускорить перенос базы данных с использованием службы:
- используйте ценовую категорию общего назначения с несколькими ЦП при создании экземпляра службы, чтобы обеспечить параллелизацию и ускоренную передачу данных благодаря использованию нескольких ЦП;
- временно увеличьте масштаб целевого экземпляра базы данных SQL Azure до SKU уровня "Премиум" во время операции переноса данных, чтобы уменьшить регулирование количества запросов базы данных SQL Azure, которое может повлиять на действия передачи данных при использовании номеров SKU низкого уровня.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>В. Как настроить виртуальную сеть Azure?
В нескольких руководствах корпорации Майкрософт приведены пошаговые инструкции по настройке виртуальной сети Azure, а официальную документацию можно найти в [этой статье](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>В. Почему моя служба Azure Database Migration Service недоступна или остановлена?
Если пользователь явно останавливает службу Azure Database Migration Service (DMS), или если она неактивна в течение 24 часов, она будет находиться в состоянии остановки или автоматической приостановки. В каждом случае служба будет недоступна и остановлена.  Чтобы возобновить активную миграцию, перезапустите службу.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>В. Где можно оставить отзыв о службе Azure Database Migration Service?
Нам очень интересно ваше мнение! Любые отзывы и идеи о службе Azure Database Migration Service можно отправить через UserVoice [здесь](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Дополнительная информация
Общие сведения о службе Azure Database Migration Service и доступности по регионам в период действия общедоступной предварительной версии см. в статье [Что такое предварительная версия службы Azure Database Migration Service?](dms-overview.md) 
