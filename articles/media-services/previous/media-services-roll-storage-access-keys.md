---
title: Обновление служб мультимедиа после оборота ключей доступа к хранилищу | Документация Майкрософт
description: В этой статье содержится руководство по обновлению служб мультимедиа после оборота ключей доступа к хранилищу.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: ebaa679c43bd577d4f37feb7f3a025086490e25f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785753"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Обновление служб мультимедиа после оборота ключей доступа к хранилищу

При создании новой учетной записи служб мультимедиа Azure (AMS) пользователю предлагается выбрать учетную запись хранения Azure, которая используется для хранения мультимедийного содержимого. К учетной записи служб мультимедиа можно добавить несколько учетных записей хранения. В этом разделе показано, как чередовать ключи к хранилищу данных. В нем также показано, как добавлять учетные записи хранения в учетную запись служб мультимедиа. 

Чтобы выполнить действия, описанные в этом разделе, необходимо использовать [интерфейсы API ARM](https://docs.microsoft.com/rest/api/media/mediaservice) и [PowerShell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Дополнительные сведения см. в разделе [Использование Azure PowerShell с диспетчером ресурсов Azure](../../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Обзор

При создании новой учетной записи хранения служба Azure создает два 512-разрядных ключа доступа к хранилищу, используемые для проверки подлинности во время доступа к вашей учетной записи хранения. Для безопасности подключений к хранилищу рекомендуется периодически повторно создавать и менять ключи доступа к хранилищу. Два ключа доступа (основной и дополнительный) дают возможность поддерживать подключение к учетной записи хранения с помощью одного ключа доступа в то время, как выполняется повторное создание второго ключа доступа. Этот процесс называется "оборот ключей доступа".

Службы мультимедиа зависят от предоставленного ключа к хранилищу данных. В частности, от ключа доступа к хранилищу зависят указатели, используемые для потоковой передачи или скачивания ресурсов-контейнеров. При создании учетная запись AMS по умолчанию зависит от ключа доступа к основному хранилищу, но пользователь может обновить ключ хранилища в AMS. Необходимо указать службам мультимедиа, какой ключ использовать, выполнив шаги, описанные в данном разделе.  

>[!NOTE]
> Если используется несколько учетных записей хранения, необходимо выполнить эту процедуру для каждой из них. Порядок, в котором следует чередовать ключи к хранилищу данных, не является фиксированным. Можно сначала сменить вторичный ключ, а затем первичный ключ, или наоборот.
>
> Перед выполнением описанных в этом разделе действий с рабочей учетной записью попробуйте выполнить их на подготовительной учетной записи.
>

## <a name="steps-to-rotate-storage-keys"></a>Инструкции по чередованию ключей к хранилищу данных 
 
 1. Измените первичный ключ учетной записи хранения с помощью командлета PowerShell или портала [Azure](https://portal.azure.com/).
 2. Вызовите командлет Sync-AzureRmMediaServiceStorageKeys с соответствующими параметрами, чтобы принудительно применить к учетной записи служб мультимедиа ключи учетной записи хранения.
 
    Следующий пример демонстрирует, как синхронизировать ключи с учетными записями хранения.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Подождите один час или около того. Проверьте работу потоковой передачи там, где она используется.
 4. Измените вторичный ключ учетной записи хранения с помощью командлета PowerShell или портала Azure.
 5. Вызовите командлет PowerShell Sync-AzureRmMediaServiceStorageKeys с соответствующими параметрами, чтобы принудительно применить к учетной записи служб мультимедиа новые ключи учетной записи хранения. 
 6. Подождите один час или около того. Проверьте работу потоковой передачи там, где она используется.
 
### <a name="a-powershell-cmdlet-example"></a>Примеры использования командлетов PowerShell 

Ниже приведен пример, демонстрирующий, как получить учетную запись хранилища и синхронизировать ее с учетной записью AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Инструкции по добавлению учетных записей хранения в учетную запись AMS

В следующем разделе показано, как добавить учетные записи хранения в учетную запись AMS: [Управление активами служб мультимедиа в нескольких учетных записях хранения](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Благодарности
Мы выражаем признательность тем, кто помог нам в составлении этого документа, — это Сенк Динджилоглу (Cenk Dingiloglu), Милан Гада (Milan Gada) и Сева Титов.
