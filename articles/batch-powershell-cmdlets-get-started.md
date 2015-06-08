<properties
	pageTitle="Приступая к работе с командлетами Azure PowerShell"
	description="Предоставляет командлеты Azure PowerShell, используемые для управления Пакетной службой Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="yidingz"/>

<tags
	ms.service="batch"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="04/15/2015"
	ms.author="danlep"/>

# Приступая к работе с командлетами Azure PowerShell
Эта статья представляет краткое введение в командлеты Azure PowerShell, которые можно использовать для управления учетными записями Пакетной службы и получения сведений о рабочих элементах службы, ее заданиях и задачах.

Подробный синтаксис командлета: введите ```get-help <Cmdlet_name>```.


## Предварительные требования

* **Предварительная версия Пакетной службы** – для работы со службой зарегистрируйтесь в [Предварительной версии Пакетной службы](https://account.windowsazure.com/PreviewFeatures), если вы этого еще не сделали.
* **Azure PowerShell** – см. раздел [Как установить и настроить Azure PowerShell](powershell-install-configure.md), чтобы узнать предварительные требования, и получить инструкции по скачиванию и установке. Командлеты Пакетной службы появились в версии 0.8.10 и более поздних версиях.

## Использование командлетов Пакетной службы

Чтобы запустить Azure PowerShell и [подключиться к подпискам Azure](powershell-install-configure.md#Connect), выполните стандартные действия. Кроме этого:

* **Выберите подписку Azure** — если у вас есть больше одной подписки, выберите подписку, в которую вы добавили компонент предварительной версии Пакетной службы:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Переключиться в режим AzureResourceManage** – командлеты Пакетной службы поставляются в модуле диспетчера ресурсов Azure. Подробнее об этом см. [Использование Windows PowerShell с диспетчером ресурсов](powershell-azure-resource-manager.md). Чтобы использовать этот модуль, запустите командлет [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx):

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Управление учетными записями пакетной службы и ключами

Чтобы создавать и управлять учетными записями пакетной службы и ключами, можно использовать командлеты Azure PowerShell.

### Создание учетной записи Пакетной службы

Командлет **New-AzureBatchAccount** создает новую учетную запись Пакетной службы в группе указанного ресурса. Если у вас еще нет группы ресурсов, создайте ее. Для этого выполните командлет [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx), указав один из регионов Azure в параметре **Расположение**. (Регионы, в которых доступны различные ресурсы Azure, можно найти, выполнив командлет [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Например:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Затем создайте новую учетную запись Пакетной службы в группе ресурсов, указав имя учетной записи в < * account_name * > и расположение, где доступна Пакетная служба. Создание учен=тной записи может занять несколько минут. Например:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Имя учетной записи Пакетной службы в Azure должно быть уникальным, содержать от 3 до 24 символов и состоять только из строчных букв и цифр.

### Получение ключей доступа к учетной записи
**Get-AzureBatchAccountKeys** выводит клавиши доступа, связанные с учетной записью Пакетной службы Azure. Например, выполните следующую команду, чтобы получить первичные и вторичные ключи созданной учетной записи.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Создание нового ключа доступа
**New-AzureBatchAccountKey** создает новый первичный или вторичный ключ учетной записи Пакетной службы Azure. Например, чтобы создать новый первичный ключ для учетной записи Пакетной службы, введите:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Чтобы создать новый вторичный ключ, введите "Вторичный" в параметре **KeyType**. Повторно создавать первичный и вторичный ключи нужно отдельно.

### Удаление учетной записи Пакетной службы
**Remove-AzureBatchAccount** удаляет учетную запись Пакетной службы. Например:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

При появлении запроса на удаление учетной записи подтвердите удаление. Удаление учетной записи может занять некоторое время.

## Запрос рабочих элементов, заданий и задач

Чтобы послать запрос на сущности, созданные в учетной записи воспользуйтесь командлетами **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask** и **Get-AzureBatchPool**.

Чтобы использовать эти командлеты, сначала нужно создать объект AzureBatchContext для хранения имени учетной записи и ключей:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Передайте этот контекст в командлеты, которые взаимодействуют с Пакетной службой, через параметр **BatchContext**.

> [AZURE.NOTE]По умолчанию первичный ключ учетной записи используется для проверки подлинности, но вы можете явно выбрать ключ, который нужно использовать, изменив свойство **KeyInUse** объекта BatchAccountContext: ```$context.KeyInUse = "Secondary"```.


### Запрос данных

Например, чтобы найти свои рабочие элементы, выполните **Get-AzureBatchWorkItem**. По умолчанию это запрос всех рабочих элементов в вашей учетной записи при условии, что вы уже сохранили объект BatchAccountContext в *$context*:

```
Get-AzureBatchWorkItem -BatchContext $context
```

Это можно делать с другими сущностями, например пулами:

```
Get-AzureBatchPool -BatchContext $context
```
### Использование фильтра OData

Чтобы найти объекты, которые вас интересуют, установите фильтр OData в параметре **Фильтр**. Например, можно найти все рабочие элементы с именами, начинающимися с «myWork»:

```
$filter = "startswith(name,'myWork') and state eq 'active'" 
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
``` 

Этот способ не такой гибкий, как использование «Where-Object» в локальном конвейере. Но запрос отправляется непосредственно Пакетной службе, чтобы вся фильтрация выполнялась на стороне сервера, сохраняя пропускную способность Интернета.

### Использование параметра «Имя»

Альтернатива фильтру OData – использование параметра **Имя**. Отправить запрос на конкретный рабочий элемент с именем «myWorkItem» можно так:

``` 
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context 

```
Параметр **Имя** поддерживает поиск только полного имени без подстановочных знаков или фильтров в стиле OData.

### Использование конвейера

Командлеты Пакетной службы могут использовать конвейер PowerShell для передачи данных между командлетами. Это имеет тот же эффект, что и указание параметра, но упрощает вывод нескольких сущностей. Например, вы можете найти все задачи в своей учетной записи:

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context 
```

### Использование параметра MaxCount

По умолчанию каждый командлет возвращает максимум 1000 объектов. Если этот предел достигнут, вы можете сузить свой фильтр для возврата меньшего количества объектов или явно задать максимальное использование параметра **MaxCount**. Например:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context 

```

Чтобы удалить верхнюю границу, задайте **MaxCount** значение «0» или меньше.

## Связанные разделы
* [Технический обзор Пакетной службы Azure](batch-technical-overview.md)
* [Скачать Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)
* [Справка по командлетам Azure](https://msdn.microsoft.com/library/jj554330.aspx)

<!---HONumber=GIT-SubDir-->