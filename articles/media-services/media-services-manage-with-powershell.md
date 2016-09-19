<properties 
	pageTitle="Управление учетными записями служб мультимедиа Azure с помощью PowerShell" 
	description="Узнайте, как управлять учетными записями служб мультимедиа Azure с помощью командлетов PowerShell." 
	authors="Juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

.<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2016"
	ms.author="juliako"/>


#Управление учетными записями служб мультимедиа Azure с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Для создания учетной записи служб мультимедиа Azure необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Бесплатная пробная версия Azure</a>.

##Обзор 

В этой статье перечислены командлеты Azure PowerShell для служб мультимедиа Azure (AMS), доступные в инфраструктуре Azure Resource Manager. Командлеты существуют в пространстве имен **Microsoft.Azure.Commands.Media**.

## Версии

**Версия API**: от 01.10.2015.
               

## New-AzureRmMediaService

Создает службу мультимедиа.

### Синтаксис

Набор параметров: StorageAccountIdParamSet.

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Набор параметров: StorageAccountsParamSet.

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы | Нет
---|---
Обязательный? | Да
Позиция? | 0
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Name (Имя)
---|---
Обязательный? |Да
Позиция? |1
Значение по умолчанию |Нет
Принимает входные данные конвейера? |нет
Принимает подстановочные знаки? |нет

**-Location &lt;Строка&gt;**

Указывает расположение ресурсов для этой службы мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |2
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-StorageAccountId &lt;Строка&gt;**

Указывает основную учетную запись хранения, связанную с этой службой мультимедиа.

- Поддерживаются только новые учетные записи хранения (созданные с помощью API Resource Manager).

- Учетная запись хранения должна существовать и находиться в том же расположении, что и служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |3
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Имя набора параметров |StorageAccountIdParamSet
Принимает подстановочные знаки?|нет

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Указывает учетные записи хранения, связанные с этой службой мультимедиа.

- Поддерживаются только новые учетные записи хранения (созданные с помощью API Resource Manager).

- Учетная запись хранения должна существовать и находиться в том же расположении, что и служба мультимедиа.

- Можно указать только одну основную учетную запись хранения.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |3
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Имя набора параметров |StorageAccountsParamSet
Принимает подстановочные знаки? |нет

**-Tags &lt;хэш-таблица&gt;**

Указывает хэш-таблицу тегов, которые связаны с этой службой мультимедиа.

- Пример: @{"тег1"="значение1";"тег2"="значение2"}

Псевдонимы |Нет
---|---
Обязательный? |нет
Позиция? |именованная
Значение по умолчанию |Нет
Принимает входные данные конвейера? |нет
Принимает подстановочные знаки? |нет

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Set-AzureRmMediaService

Обновляет службу мультимедиа.

### Синтаксис

	Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |0
Значение по умолчанию |Нет
Принимает конвейерный ввод? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Name (Имя)
---|---
Обязательный? |Истина
Позиция? |1
Значение по умолчанию |None
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |Ложь

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Указывает учетные записи хранения, связанные с этой службой мультимедиа.

- Поддерживаются только новые учетные записи хранения (созданные с помощью API Resource Manager).

- Учетная запись хранения должна существовать и находиться в том же расположении, что и служба мультимедиа.

- Можно указать только одну основную учетную запись хранения.

Псевдонимы |Нет
---|---
Обязательный? |нет
Позиция? |Именованная
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Имя набора параметров |StorageAccountsParamSet
Принимает подстановочные знаки? |нет

**-Tags &lt;хэш-таблица&gt;**

Указывает хэш-таблицу тегов, которые связаны с этой службой мультимедиа.

- Теги, которые связаны со службой мультимедиа, заменяются значением, указанным пользователем.

Псевдонимы |Нет
---|---
Обязательный? |Ложь
Позиция? |Именованная
Значение по умолчанию |None
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Remove-AzureRmMediaService

Удаляет службу мультимедиа.

### Синтаксис

	Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |0
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |2
Значение по умолчанию |None
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |Ложь

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Get-AzureRmMediaService

Получает все службы мультимедиа в группе ресурсов или службу мультимедиа с заданным именем.

### Синтаксис

Набор параметров: ResourceGroupParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]	

Набор параметров: AccountNameParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |0
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Имя набора параметров |ResourceGroupParameterSet, AccountNameParameterSet
Принимает подстановочные знаки? Нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |1
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Имя набора параметров |AccountNameParameterSet
Принимает подстановочные знаки? |нет

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Get-AzureRmMediaServiceKeys

Возвращает ключи службы мультимедиа.

### Синтаксис

	Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |0
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |1
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Set-AzureRmMediaServiceKey

Повторно создает первичный или вторичный ключ службы мультимедиа.

### Синтаксис

	Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |0
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |1
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-KeyType &lt;тип ключа&gt;**

Указывает тип ключа этой службы мультимедиа.

- Первичный или вторичный

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |2
Значение по умолчанию |Нет
Принимает входные данные конвейера? |нет
Принимает подстановочные знаки? |нет

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Sync-AzureRmMediaServiceStorageKeys

Синхронизирует ключи учетной записи хранения, которая связана с этой службой мультимедиа.

### Синтаксис

	Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountName] <string>  [<CommonParameters>]

### Параметры

**-ResourceGroupName &lt;Строка&gt;**

Указывает имя группы ресурсов, к которой принадлежит эта служба мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |0
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-AccountName &lt;Строка&gt;**

Указывает имя этой службы мультимедиа.

Псевдонимы |Нет
---|---
Обязательный? |Да
Позиция? |1
Значение по умолчанию |Нет
Принимает входные данные конвейера? |true(ByPropertyName)
Принимает подстановочные знаки? |нет

**-StorageAccountId &lt;Строка&gt;**

Указывает учетную запись хранения, связанную с этой службой мультимедиа.

Псевдонимы |Идентификатор
---|---
Обязательный? |Да
Позиция? |2
Значение по умолчанию |Нет
Принимает входные данные конвейера? | true(ByPropertyName)
Принимает подстановочные знаки? |нет

**&lt;Параметры команды&gt;**

Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.

### Входные данные

Тип входных данных определяет тип объектов, которые можно передавать в командлет.

### Выходные данные

Тип выходных данных определяет тип объектов, которые будет выдавать командлет.

## Дальнейшие действия 

Изучите схемы обучения для служб мультимедиа.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!---HONumber=AcomDC_0907_2016-->