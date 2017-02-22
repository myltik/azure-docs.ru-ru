---
title: "Репликация глобальной базы данных DocumentDB | Документация Майкрософт"
description: "Узнайте, как управлять глобальной репликацией учетной записи DocumentDB на портале Azure."
services: documentdb
keywords: "глобальная база данных, репликация"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 691e2e9156a825e64890f035b23cb2526d502107
ms.openlocfilehash: c7d8cea9a15ec79356c89f628ef9d8e8ccbaaec3


---
# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Выполнение репликации глобальной базы данных DocumentDB с помощью портала Azure

Узнайте, как с помощью портала Azure выполнить репликацию данных в нескольких регионах, чтобы обеспечить глобальную доступность данных в Azure DocumentDB.

Сведения о том, как функционирует репликация глобальной базы данных в DocumentDB, см. в статье [Глобальное распространение данных с помощью DocumentDB](documentdb-distribute-data-globally.md). Сведения о том, как выполнить репликацию глобальной базы данных программным путем, см. в статье [Разработка с помощью учетных записей DocumentDB в нескольких регионах](documentdb-developing-with-multiple-regions.md).

> [!NOTE]
> Глобальное распределение баз данных DocumentDB является общедоступным и автоматически включается для всех вновь создаваемых учетных записей DocumentDB. Мы работаем над включением глобального распределения для всех существующих учетных записей, но до того момента, если вам необходимо включить эту функцию для своей учетной записи, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , и мы активируем ее для вас.
> 
> 

## <a name="a-idaddregionaadd-global-database-regions"></a><a id="addregion"></a>Добавление регионов глобальной базы данных
Система DocumentDB доступна в большинстве [регионов Azure][azureregions]. После выбора уровня согласованности по умолчанию для учетной записи базы данных вы можете связать один или несколько регионов (в зависимости от выбранного уровня согласованности по умолчанию и потребностей глобального распространения).

1. На навигационной панели [портала Azure](https://portal.azure.com/) щелкните **NoSQL (DocumentDB)**.
2. В колонке **NoSQL (DocumentDB)** выберите учетную запись базы данных, которую нужно изменить.
3. В меню колонки учетной записи щелкните **Глобальная репликация данных**.
4. В колонке **Глобальная репликация данных** выберите регионы для добавления или удаления и нажмите кнопку **Сохранить**. Добавление регионов оплачивается. Для получения дополнительной информации перейдите на [страницу цен](https://azure.microsoft.com/pricing/details/documentdb/) или прочитайте статью [Глобальное распространение данных с помощью DocumentDB](documentdb-distribute-data-globally.md).
   
    ![Можно щелкать регионы на карте, чтобы добавить или удалить их.][1]
    
После добавления второго региона на портале в колонке **Replicate data locally** (Локальная репликация данных) активируется параметр **Ручная отработка отказа**. Этот параметр позволяет проверить процесс отработки отказа. После добавления третьего региона в той же колонке активируется параметр **Приоритеты при отработке отказа**, позволяющий изменить порядок отработки отказов для операций чтения.  

### <a name="selecting-global-database-regions"></a>Выбор регионов глобальной базы данных
При настройке двух или более регионов рекомендуется выбирать их исходя из пар регионов, описанных в статье [Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk: пары регионов Azure][bcdr].

В частности, при настройке нескольких регионов убедитесь, что в каждом из столбцов парных регионов выбрано одинаковое количество регионов (±1 для четных и нечетных количеств). Например, если вы хотите развернуть четыре региона США, выберите два региона США в левом столбце и два в правом. Тогда подходящим набором будут следующие регионы: западная часть США, восточная часть США, северо-центральный регион США и юго-центральный регион США.

Важно следовать этим указаниям, если для сценариев аварийного восстановления настроено только два региона. Если регионов больше, то выполнение данных указаний рекомендуется, но не критично, при условии, что некоторые из выбранных регионов образуют пары.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **NoSQL (DocumentDB)** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a name="a-idnextanext-steps"></a><a id="next"></a>Дальнейшие действия
Сведения об управлении согласованностью глобально реплицируемой учетной записи см. в статье [Уровни согласованности в DocumentDB](documentdb-consistency-levels.md).

Сведения о том, как функционирует репликация глобальной базы данных в DocumentDB, см. в статье [Глобальное распространение данных с помощью DocumentDB](documentdb-distribute-data-globally.md). Сведения о том, как программным путем выполнить репликацию данных в нескольких регионах, см. в статье [Разработка с помощью учетных записей DocumentDB в нескольких регионах](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO4-->


