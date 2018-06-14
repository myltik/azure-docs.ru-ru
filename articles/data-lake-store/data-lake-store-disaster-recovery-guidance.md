---
title: Руководство по аварийному восстановлению Azure Data Lake Store | Документация Майкрософт
description: Руководство по аварийному восстановлению Azure Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 7401355c7920729933d0fcc3dd4cc8ce610c399e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199081"
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>Руководство по аварийному восстановлению данных в Data Lake Store

Azure Data Lake Store предоставляет локально избыточное хранилище (LRS). Таким образом, благодаря автоматическим репликам данные в учетной записи Azure Data Lake Store устойчивы к временным сбоям оборудования в регионе. Это гарантирует устойчивость и высокий уровень доступности данных, а также соответствие требованиям соглашения об уровне обслуживания Azure Data Lake Store. В этой статье приведены рекомендации по защите данных в случае редких сбоев оборудования в регионе или случайного удаления.

## <a name="disaster-recovery-guidance"></a>Руководство по аварийному восстановлению
Крайне важно, чтобы каждый клиент подготовил свой собственный план аварийного восстановления. Ознакомьтесь со сведениями в этой статье, чтобы составить свой план аварийного восстановления. Ниже приведены некоторые ресурсы, которые помогу составить план аварийного восстановления.

* [Аварийное восстановление и высокий уровень доступности для приложений Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Техническое руководство по обеспечению устойчивости в Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Рекомендации
Мы рекомендуем копировать важные данные в другую учетную запись Data Lake Store, расположенную в другом регионе. Выполняйте эту процедуру так часто, как того требует ваш план аварийного восстановления. Копировать данные можно несколькими способами, в том числе с помощью [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) или [фабрики данных Azure](../data-factory/connector-azure-data-lake-store.md). Фабрика данных Azure — это полезная служба для создания и развертывания конвейеров перемещения данных на регулярной основе.

В случае регионального сбоя пользователь может получить доступ к копиям данных, расположенным в другом регионе. С помощью [панели мониторинга работоспособности службы Azure](https://azure.microsoft.com/status/) можно определить глобальное состояние службы Azure.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Руководство по восстановлению данных после повреждения или случайного удаления
Хотя за счет автоматических реплик Azure Data Lake Store и обеспечивает устойчивость данных, они по-прежнему не защищены от повреждения или случайного удаления в приложении (а также разработчиками или пользователями).

### <a name="best-practices"></a>Рекомендации
Чтобы предотвратить случайное удаление данных, для начала мы рекомендуем настроить для своей учетной записи Data Lake Store правильные политики доступа,  в том числе применение [блокировок ресурсов Azure](../azure-resource-manager/resource-group-lock-resources.md) для блокировки важных ресурсов, а также контроль доступа на уровне учетной записи и файлов с помощью [компонентов обеспечения безопасности Data Lake Store](data-lake-store-security-overview.md). Кроме того, мы советуем регулярно создавать копии важных данных в других учетных записях Data Lake Store, папках и подписках Azure с помощью [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) или [фабрики данных Azure](../data-factory/connector-azure-data-lake-store.md).  Таким образом вы сможете восстановить данные после повреждения или случайного удаления. Фабрика данных Azure — это полезная служба для создания и развертывания конвейеров перемещения данных на регулярной основе.

Организации могут также включить [ведение журнала диагностики](data-lake-store-diagnostic-logs.md) для своих учетных записей Azure Data Lake Store. Это позволит собирать журналы аудита доступа к данным, содержащие сведения о пользователях, которые могли удалить или обновить файл.

## <a name="next-steps"></a>Дополнительная информация
* [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md)
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)

