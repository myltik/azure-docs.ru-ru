---
title: Репликация данных в службе хранилища Azure | Документация Майкрософт
description: Данные в учетной записи хранения Microsoft Azure реплицируются для обеспечения устойчивости и высокого уровня доступности. Варианты репликации включают локально избыточное хранилище (LRS), хранилище, избыточное в пределах зоны (ZRS), геоизбыточное хранилище (GRS) и геоизбыточное хранилище с доступом для чтения (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 6c2c6979d56eb19ff2ba4fb647c7c51e52e51ac6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076220"
---
# <a name="azure-storage-replication"></a>Репликация службы хранилища Azure

Данные в учетной записи хранения Microsoft Azure всегда реплицируются, обеспечивая устойчивость и высокий уровень доступности. При репликации службы хранилища Azure данные копируются для защиты от запланированных и незапланированных событий, включая временные сбои оборудования, сети или энергоснабжения, масштабные стихийные бедствия и т. д. Вы можете реплицировать данные в пределах одного центра обработки данных, между зональными центрами обработки данных в одном регионе или между регионами.

Репликация гарантирует соответствие учетной записи хранения [соглашению об уровне обслуживания относительно хранения данных](https://azure.microsoft.com/support/legal/sla/storage/) даже при сбоях. Сведения о гарантиях службы хранилища Azure в вопросах устойчивости и доступности см. в соглашении об уровне обслуживания.

## <a name="choosing-a-replication-option"></a>Выбор варианта репликации

При создании учетной записи хранения можно выбрать один из следующих вариантов репликации:

* [Локально избыточное хранилище (LRS)](storage-redundancy-lrs.md)
* [Хранилище, избыточное в пределах зоны (ZRS)](storage-redundancy-zrs.md)
* [Геоизбыточное хранилище (GRS)](storage-redundancy-grs.md)
* [Геоизбыточное хранилище с доступом для чтения (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

В таблице ниже представлено краткое описание области устойчивости и доступности, которую каждая стратегия репликации предоставляет для данного типа события (или события с аналогичным уровнем влияния).

| Сценарий                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Недоступность узлов в центре обработки данных                                                                 | Yes                             | Yes                              | Yes                                  | Yes                                  |
| Весь центр обработки данных (зональный или не зональный) становится недоступным                                           | Нет                               | Yes                              | Yes                                  | Yes                                  |
| Региональный сбой                                                                                     | Нет                               | Нет                                | Yes                                  | Yes                                  |
| Доступ на чтение к данным (в удаленной геореплицируемой области) в случае недоступности всего региона | Нет                               | Нет                                | Нет                                    | Yes                                  |
| Предназначено для обеспечения надежности объектов на уровне ___ в течение определенного года                                          | не менее 99,999999999 % (11 девяток) | не менее 99,9999999999 % (12 девяток) | не менее 99,99999999999999 % (16 девяток) | не менее 99,99999999999999 % (16 девяток) |
| Поддерживаемые типы учетных записей хранения                                                                   | GPv1, GPv2, большой двоичный объект                | GPv2                             | GPv1, GPv2, большой двоичный объект                     | GPv1, GPv2, большой двоичный объект                     |

На странице [Обзор цен на хранилище Azure](https://azure.microsoft.com/pricing/details/storage/) приведены сведения о различных вариантах обеспечения избыточности данных.

> [!NOTE]
> Хранилище класса Premium поддерживает только локально избыточное хранилище (LRS). Сведения о хранилище класса "Премиум" см. в статье [Хранилище класса "Премиум": высокопроизводительное хранилище для рабочих нагрузок виртуальных машин Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Изменение стратегии репликации
Изменять стратегию репликации учетной записи хранения можно с помощью [портала Azure](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) или одной из [клиентских библиотек Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). При изменении типа репликации для учетной записи хранения простоя не будет.

   > [!NOTE]
   > В настоящее время нельзя использовать портал или API для преобразования учетной записи в ZRS. Если вы хотите преобразовать репликацию своей учетной записи в ZRS, ознакомьтесь с разделом [Хранилище, избыточное в пределах зоны (ZRS). Высокодоступные приложения для службы хранилища Azure](storage-redundancy-zrs.md), чтобы узнать больше.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Нужно ли платить за изменение стратегии репликации учетной записи?
Это зависит от способа преобразования. Если расположить хранилища по цене предложения избыточности (начиная с самого дешевого и заканчивая самым дорогим), получится следующее: LRS, ZRS, GRS и RA-GRS. Например за переход *с* LRS на любое другое хранилище будет взиматься дополнительная плата, так как требуется более сложный уровень избыточности. За переход *на* GRS или RA-GRS будет взиматься дата за выходную пропускную способность, так как данные (в основном регионе) реплицируются на удаленный дополнительный регион. Это одноразовый платеж при начальной настройке. После копирования данных плата за преобразование взиматься не будет. Вы будете оплачивать только репликацию всех новых данных или обновлений до имеющихся. Сведения о стоимости пропускной способности см. на странице [цен на хранение блочных BLOB-объектов](https://azure.microsoft.com/pricing/details/storage/blobs/).

Если изменить тип репликации GRS на LRS, дополнительная плата не потребуется, но реплицированные данные удаляются из дополнительного расположения.

## <a name="see-also"></a>См. также

- [Локально избыточное хранилище (LRS). Недорогая избыточность данных для службы хранилища Azure](storage-redundancy-lrs.md)
- [Хранилище, избыточное в пределах зоны (ZRS). Высокодоступные приложения для службы хранилища Azure](storage-redundancy-zrs.md)
- [Геоизбыточное хранилище (GRS). Межрегиональная репликация для службы хранилища Azure](storage-redundancy-grs.md)
- [Целевые показатели масштабируемости и производительности службы хранилища Azure](storage-scalability-targets.md)
- [Проектирование высокодоступных приложений с использованием RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Windows Azure Storage Redundancy Options and Read Access Geo Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (Варианты избыточности и геоизбыточное хранилище с доступом для чтения службы хранилища Microsoft Azure)
- [Документ SOSP — служба хранилища Azure: высокодоступная облачная служба хранилища со строгой согласованностью](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
