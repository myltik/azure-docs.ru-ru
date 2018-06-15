---
title: Приступая к работе с модулем PowerShell пакетной службы Azure | Документация Майкрософт
description: Краткое описание командлетов Azure PowerShell, используемых для управления ресурсами пакетной службы.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d263dbb89b10876c3a1deb330f969c598addd6a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593605"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Управление ресурсами пакетной службы с помощью командлетов PowerShell

С помощью командлетов PowerShell пакетной службы Azure можно выполнять те же задачи, которые выполняются с помощью API-интерфейсов пакетной службы, портала Azure и интерфейса командной строки (CLI). Эта статья содержит краткие сведения о командлетах, используемых для управления учетными записями пакетной службы и работы с ее ресурсами, включая пулы, задания и задачи.

Полный список командлетов пакетной службы Azure и их синтаксис см. в [этой справке](/powershell/module/azurerm.batch/#batch).

В этой статье описаны командлеты Azure PowerShell 3.0.0. Рекомендуется часто обновлять Azure PowerShell, чтобы пользоваться всеми преимуществами службы.

## <a name="prerequisites"></a>предварительным требованиям
Чтобы управлять ресурсами пакетной службы с помощью Azure PowerShell, выполните следующие действия.

* [Установка и настройка Azure PowerShell](/powershell/azure/overview)
* Выполните командлет **Connect-AzureRmAccount**, чтобы подключиться к своей подписке (командлеты пакетной службы Azure входят в состав модуля Azure Resource Manager):
  
    `Connect-AzureRmAccount`
* **Зарегистрируйте пространство имен поставщика пакетной службы.** Эту операцию достаточно выполнить **раз для каждой подписки**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Управление учетными записями пакетной службы и ключами
### <a name="create-a-batch-account"></a>Создание учетной записи Пакетной службы
Командлет **New-AzureRmBatchAccount** создает учетную запись пакетной службы в указанной группе ресурсов. Если у вас еще нет группы ресурсов, создайте ее, выполнив командлет [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Укажите один из регионов Azure в параметре **Location**, например Central US. Например: 

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Затем создайте учетную запись пакетной службы в группе ресурсов, указав имя учетной записи вместо <*account_name*>, а также расположение и имя группы ресурсов. Создание учетной записи пакетной службы может занять некоторое время. Например: 

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Имя учетной записи пакетной службы должно быть уникальным для региона Azure группы ресурсов, содержать от 3 до 24 символов и состоять только из строчных букв и цифр.
> 
> 

### <a name="get-account-access-keys"></a>Получение ключей доступа к учетной записи
**Get-AzureRmBatchAccountKeys** выводит клавиши доступа, связанные с учетной записью пакетной службы Azure. Например, выполните следующую команду, чтобы получить первичные и вторичные ключи созданной учетной записи.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Создание нового ключа доступа
**New-AzureRmBatchAccountKey** создает новый первичный или вторичный ключ учетной записи пакетной службы Azure. Например, чтобы создать новый первичный ключ для учетной записи Пакетной службы, введите:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Чтобы создать новый вторичный ключ, введите "Вторичный" в параметре **KeyType** . Повторно создавать первичный и вторичный ключи нужно отдельно.
> 
> 

### <a name="delete-a-batch-account"></a>Удаление учетной записи Пакетной службы
**Remove-AzureRmBatchAccount** удаляет учетную запись пакетной службы. Например: 

    Remove-AzureRmBatchAccount -AccountName <account_name>

При появлении запроса на удаление учетной записи подтвердите удаление. Удаление учетной записи может занять некоторое время.

## <a name="create-a-batchaccountcontext-object"></a>Создание объекта BatchAccountContext
Чтобы выполнить проверки подлинности с помощью командлетов PowerShell пакетной службы во время создания пулов, заданий, задач и других ресурсов, а также управления ими, вам необходимо сначала создать объект BatchAccountContext для хранения ключей и имени учетной записи.

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Передайте объект BatchAccountContext в командлеты, которые используют параметр **BatchContext** .

> [!NOTE]
> По умолчанию первичный ключ учетной записи используется для проверки подлинности, но вы можете явно выбрать ключ, который нужно использовать, изменив свойство **KeyInUse** объекта BatchAccountContext: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Создание и изменение ресурсов пакетной службы
Чтобы создать ресурсы в учетной записи пакетной службы, используйте командлеты **New-AzureBatchPool**, **New-AzureBatchJob** и **New-AzureBatchTask**. Чтобы обновить свойства ресурсов в учетной записи пакетной службы, используйте соответствующие командлеты **Get-** и **Set-**, а чтобы удалить ресурсы — командлет **Remove-**.

При использовании этих командлетов вам нужно не только передать объект BatchContext, но также создать или передать объекты, которые содержат параметры с подробными настройками ресурсов, как показано в следующем примере. Дополнительные примеры доступны в справочных материалах по каждому командлету.

### <a name="create-a-batch-pool"></a>Создание пула пакетной службы
Создавая или обновляя пул пакетной службы, вам нужно выбрать конфигурацию облачной службы или виртуальной машины для операционной системы на вычислительных узлах (см. [обзор функций пакетной службы](batch-api-basics.md#pool)). Если вы указываете конфигурацию облачной службы, образы вычислительных узлов будут созданы на основе одного из [выпуска гостевых ОС Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Если вы указываете конфигурацию виртуальной машины, можно выбрать один из поддерживаемых образов виртуальных машин Linux или Windows, доступных в [магазине виртуальных машин Azure Marketplace][vm_marketplace], или предоставить настраиваемый образ, подготовленный вами.

При выполнении командлета **New-AzureBatchPool** передайте параметры операционной системы в объекте PSCloudServiceConfiguration или PSVirtualMachineConfiguration. Например, следующий командлет создает новый пул пакетной службы с вычислительными узлами малого размера в конфигурации облачной службы. При этом образ создан с использованием последней версии операционной системы семейства 3 (Windows Server 2012). Здесь параметр **CloudServiceConfiguration** определяет переменную *$configuration* как объект PSCloudServiceConfiguration. Параметр **BatchContext** определяет ранее заданную переменную *$context* как объект BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Целевое число вычислительных узлов в новом пуле определяется по формуле автоматического масштабирования. В этом случае формула выглядит так: **$TargetDedicated=4**. Это значит, что в пуле будет не более 4 вычислительных узлов.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Запрос на получение сведений о пулах, заданиях, задачах и другой информации
Чтобы отправить запрос о сущностях, созданных в учетной записи пакетной службы, используйте командлеты **Get-AzureBatchPool**, **Get-AzureBatchJob** и **Get-AzureBatchTask**.

### <a name="query-for-data"></a>Запрос данных
Например, для поиска пулов используйте **Get AzureBatchPools** . По умолчанию этот командлет опрашивает все пулы вашей учетной записи при условии, что вы уже сохранили объект BatchAccountContext в *$context*.

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Использование фильтра OData
Чтобы найти объекты, которые вас интересуют, установите фильтр OData в параметре **Фильтр** . Например, можно найти все пулы с идентификаторами, начинающимися с myPool:

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Этот способ не такой гибкий, как использование "Where-Object" в локальном конвейере. Но запрос отправляется непосредственно Пакетной службе, чтобы вся фильтрация выполнялась на стороне сервера, сохраняя пропускную способность Интернета.

### <a name="use-the-id-parameter"></a>Используйте параметр Id
Альтернативой использования фильтра OData является использование параметра **Id** . Для запроса конкретного пула с идентификатором myPool сделайте следующее.

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Параметр **Id** поддерживает поиск только полного идентификатора без подстановочных знаков или фильтров в стиле OData.

### <a name="use-the-maxcount-parameter"></a>Использование параметра MaxCount
По умолчанию каждый командлет возвращает максимум 1000 объектов. Если этот предел достигнут, уточните параметры фильтра, чтобы он возвращал меньшее количество объектов, или явно задайте максимальное значение с помощью параметра **MaxCount** . Например: 

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Чтобы удалить верхнюю границу, задайте **MaxCount** значение "0" или меньше.

### <a name="use-the-powershell-pipeline"></a>Использование конвейера PowerShell
Командлеты Пакетной службы могут использовать конвейер PowerShell для передачи данных между командлетами. Вы получаете тот же результат, что и при указании параметра, но работа с несколькими сущностями упрощается.

Например, поиск и вывод всех задач в учетной записи:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Перезапуск (перезагрузка) каждого вычислительного узла в пуле:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Управление пакетами приложений
Пакеты приложений упрощают развертывание приложений на вычислительных узлах пулов. С помощью командлетов PowerShell для пакетной службы можно передать пакеты приложений в учетной записи пакетной службы и управлять ими, а также развертывать версии пакетов в вычислительных узлах.

**Создайте** приложение:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Добавьте** пакет приложения:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Задайте для приложения **версию по умолчанию**:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Выведите список** пакетов приложения:

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Удалите** пакет приложения:

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Удалите** приложение:

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Прежде чем удалять приложения, удалите все версии пакетов приложения. При попытке удалить приложение, в котором в настоящий момент содержатся пакеты приложения, появится ошибка о конфликте.
> 
> 

### <a name="deploy-an-application-package"></a>Развертывание пакета приложения
Вы можете указать один или несколько пакетов приложений для развертывания при создании пула. Когда вы указываете пакет во время создания пула, он развертывается на каждом из узлов, присоединяющихся к пулу. Кроме того, развертывание пакетов выполняется при перезагрузке узла или пересоздании образа узла.

При создании пула укажите параметр `-ApplicationPackageReference`, чтобы развернуть пакет приложения для узлов во время их присоединения к пулу. Сначала создайте объект **PSApplicationPackageReference** и настройте для него идентификатор и версию пакета приложения, которую нужно развернуть в вычислительных узлах пула:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Теперь создайте пул и укажите ссылочный объект пакета в качестве аргумента в параметре `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Дополнительные сведения о пакетах приложений см. в статье [Развертывание приложений на вычислительных узлах с помощью пакетов приложений пакетной службы](batch-application-packages.md).

> [!IMPORTANT]
> Чтобы использовать пакеты приложений, вам сначала нужно [связать учетную запись хранения Azure](#linked-storage-account-autostorage) со своей учетной записью пакетной службы.
> 
> 

### <a name="update-a-pools-application-packages"></a>Обновление пакетов приложений пула
Чтобы обновить приложения, назначенные существующему пулу, сначала создайте объект PSApplicationPackageReference с нужными свойствами (идентификатор и версия пакета приложения):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Затем извлеките нужный пул из пакетной службы, очистите все существующие пакеты, добавьте ссылку на новый пакет и обновите пакетную службу с использованием новых параметров пула:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Свойства пула в пакетной службе обновлены. Чтобы фактически развернуть новый пакет приложения на вычислительных узлах в пуле, необходимо перезапустить эти узлы или пересоздать образы для них. Чтобы перезапустить каждый узел в пуле, используйте следующую команду:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> На вычислительных узлах в пуле можно развернуть несколько пакетов приложений. Если нужно *добавить* пакет приложения, а не заменить развернутые пакеты, не указывайте строку `$pool.ApplicationPackageReferences.Clear()`, приведенную выше.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* Подробные сведения о синтаксисе командлетов и их примеры см. в [справке по командлетам пакетной службы Azure](/powershell/module/azurerm.batch/#batch).
* Дополнительные сведения о приложениях и пакетах приложений в пакетной службе см. в статье [Развертывание приложений на вычислительных узлах с помощью пакетов приложений пакетной службы](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
