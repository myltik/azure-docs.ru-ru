---
title: "Управление учетными записями служб мультимедиа Azure с помощью PowerShell"
description: "Узнайте, как управлять учетными записями служб мультимедиа Azure с помощью командлетов PowerShell."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2549c876ee35d6a7fa425d43e2f86d24131ca791
ms.openlocfilehash: 3d999d9e27844bc0164cc3572522b9ec022118a1


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>Управление учетными записями служб мультимедиа Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Для создания учетной записи служб мультимедиа Azure необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Бесплатная пробная версия Azure</a>.
> 
> 

## <a name="overview"></a>Обзор
В этой статье перечислены командлеты Azure PowerShell для служб мультимедиа Azure (AMS), доступные в инфраструктуре Azure Resource Manager. Командлеты существуют в пространстве имен **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Версии
**ApiVersion**: от 01.10.2015.

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
Создает службу мультимедиа.

### <a name="syntax"></a>Синтаксис
Набор параметров: StorageAccountIdParamSet.

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Набор параметров: StorageAccountsParamSet.

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Name (Имя) |
| --- | --- |
| Обязательный? |Да |
| Позиция? |1 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |нет |
| Принимает подстановочные знаки? |нет |

**-Location &lt;String&gt;**

Указывает расположение ресурсов для этой службы мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |2 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-StorageAccountId &lt;String&gt;**

Указывает основную учетную запись хранения, связанную с этой службой мультимедиа.

* Поддерживаются только новые учетные записи хранения (созданные с помощью API Resource Manager).
* Учетная запись хранения должна существовать и находиться в том же расположении, что и служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |3 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Имя набора параметров |StorageAccountIdParamSet |
| Принимает подстановочные знаки? |нет |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Указывает учетные записи хранения, связанные с этой службой мультимедиа.

* Поддерживаются только новые учетные записи хранения (созданные с помощью API Resource Manager).
* Учетная запись хранения должна существовать и находиться в том же расположении, что и служба мультимедиа.
* Можно указать только одну основную учетную запись хранения.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |3 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Имя набора параметров |StorageAccountsParamSet |
| Принимает подстановочные знаки? |нет |

**-Tags &lt;Hashtable&gt;**

Указывает хэш-таблицу тегов, которые связаны с этой службой мультимедиа.

* Пример: @{"tag1"="value1";"tag2"=:value2"}

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |нет |
| Позиция? |именованная |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |нет |
| Принимает подстановочные знаки? |нет |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
Обновляет службу мультимедиа.

### <a name="syntax"></a>Синтаксис
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Name (Имя) |
| --- | --- |
| Обязательный? |Да |
| Позиция? |1 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |Ложь |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Указывает учетные записи хранения, связанные с этой службой мультимедиа.

* Поддерживаются только новые учетные записи хранения (созданные с помощью API Resource Manager).
* Учетная запись хранения должна существовать и находиться в том же расположении, что и служба мультимедиа.
* Можно указать только одну основную учетную запись хранения.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |нет |
| Позиция? |именованная |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Имя набора параметров |StorageAccountsParamSet |
| Принимает подстановочные знаки? |нет |

**-Tags &lt;Hashtable&gt;**

Указывает хэш-таблицу тегов, которые связаны с этой службой мультимедиа.

* Теги, которые связаны со службой мультимедиа, заменяются значением, указанным пользователем.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |нет |
| Позиция? |именованная |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
Удаляет службу мультимедиа.

### <a name="syntax"></a>Синтаксис
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |2 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |Ложь |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
Получает все службы мультимедиа в группе ресурсов или службу мультимедиа с заданным именем.

### <a name="syntax"></a>Синтаксис
Набор параметров: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

Набор параметров: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Имя набора параметров |ResourceGroupParameterSet, AccountNameParameterSet |

Принимает подстановочные знаки?   нет

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |1 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Имя набора параметров |AccountNameParameterSet |
| Принимает подстановочные знаки? |нет |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
Возвращает ключи службы мультимедиа.

### <a name="syntax"></a>Синтаксис
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |1 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
Повторно создает первичный или вторичный ключ службы мультимедиа.

### <a name="syntax"></a>Синтаксис
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |1 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-KeyType &lt;KeyType&gt;**

Указывает тип ключа этой службы мультимедиа.

* Первичный или вторичный

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |2 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |нет |
| Принимает подстановочные знаки? |нет |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
Синхронизирует ключи учетной записи хранения, которая связана с этой службой мультимедиа.

### <a name="syntax"></a>Синтаксис
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Параметры
**-ResourceGroupName &lt;String&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |0 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-AccountName &lt;String&gt;**

Указывает имя этой службы мультимедиа.

| Псевдонимы | Нет |
| --- | --- |
| Обязательный? |Да |
| Позиция? |1 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**-StorageAccountId &lt;String&gt;**

Указывает учетную запись хранения, связанную с этой службой мультимедиа.

| Псевдонимы | Идентификатор |
| --- | --- |
| Обязательный? |Да |
| Позиция? |2 |
| Значение по умолчанию |Нет |
| Принимает входные данные конвейера? |true(ByPropertyName) |
| Принимает подстановочные знаки? |нет |

**&lt;CommandParameters&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### <a name="inputs"></a>Входные данные
Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### <a name="outputs"></a>Выходные данные
Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## <a name="next-step"></a>Дальнейшие действия
Изучите схемы обучения для служб мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


