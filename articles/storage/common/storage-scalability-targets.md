---
title: Целевые показатели масштабируемости и производительности службы хранилища Azure | Документация Майкрософт
description: Узнайте подробнее о целевых показателях масштабируемости и производительности службы хранилища Azure, включая показатели объема, частоты запросов, входящей и исходящей пропускной способности, как для стандартных учетных записей хранения, так и для учетных записей хранения класса Premium. Понимание целевых показателей производительности для разделов каждой из служб хранилища Azure.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: e393bb9e7615b893699caf5a931ede5803046892
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239690"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Целевые показатели масштабируемости и производительности службы хранилища Azure
## <a name="overview"></a>Обзор
Эта статья посвящена вопросам масштабируемости и производительности службы хранилища Azure. Сведения о других ограничениях Azure приведены в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md).

> [!NOTE]
> Все учетные записи хранения функционируют в новой плоской сетевой топологии и поддерживают описанные ниже целевые показатели масштабируемости и производительности независимо от того, когда они были созданы. Дополнительные сведения о неструктурированной сетевой архитектуре службы хранилища Azure и масштабируемости см. в разделе [Хранилище Microsoft Azure: доступная служба облачного хранения с развитой системой согласованности](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Приведенные целевые показатели производительности и масштабируемости предельно высоки, но достижимы. В любом случае частота запросов, с которой успешно справляется учетная запись хранения, и ее пропускная способность зависят от размера хранимых объектов, используемых схем доступа и типа рабочей нагрузки приложения. Обязательно протестируйте службу для определения соответствия ее производительности необходимым требованиям. По возможности избегайте внезапных пиковых нагрузок по трафику. Убедитесь в том, что трафик соответствующим образом распределяется по разделам.
> 
> Когда при работе приложения достигается предельная рабочая нагрузка на раздел, служба хранилища Azure начинает выдавать код ошибки 503 (сервер занят) или 500 (время ожидания операции истекло). В этом случае при повторных попытках приложение должно использовать политику экспоненциального откладывания. Экспоненциальное откладывание позволяет уменьшить нагрузку на раздел и облегчить обработку им пикового трафика. 
> 
> 

Если предельно достижимых показателей масштабируемости для одной учетной записи хранения недостаточно для работы приложения, то при разработке такого приложения его можно настроить на использование нескольких учетных записей и распределить объекты данных между ними. Дополнительные сведения о ценах см. на странице [Цены на хранилища Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="scalability-targets-for-a-storage-account"></a>Целевые показатели масштабируемости для учетной записи хранения
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Целевые показатели масштабируемости хранилища BLOB-объектов Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Целевые показатели масштабируемости службы файлов Azure
Дополнительные сведения о целевых показателях масштабируемости и производительности для службы файлов Azure и службы синхронизации файлов Azure см. в [этой статье](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Целевые показатели масштабируемости службы "Синхронизация файлов Azure"
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Целевые показатели масштабируемости хранилища очередей Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Целевые показатели масштабируемости табличного хранилища Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>См. также
* [Сведения о ценах на хранилище](https://azure.microsoft.com/pricing/details/storage/)
* [Лимиты, квоты и ограничения подписки и обслуживания Azure](../../azure-subscription-service-limits.md)
* [Репликация хранилища Azure](../storage-redundancy.md)
* [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](../storage-performance-checklist.md)
* [Хранилище Azure: доступная служба облачного хранения со строгой согласованностью](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

