---
title: "Использование Azure PowerShell со службой хранилища Azure | Документация Майкрософт"
description: "Узнайте, как использовать командлеты Azure PowerShell для службы хранилища Azure для создания и управления учетными записями хранения; для работы с большими двоичными объектами, таблицами, очередями и файлами; для настройки и запроса аналитики хранилища, а также для создания подписанных URL-адресов"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 87a116111d085fe2913bf6f5f8751c3ff5f3c076
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="using-azure-powershell-with-azure-storage"></a>Использование Azure PowerShell со службой хранилища Azure
## <a name="overview"></a>Обзор
Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Это оболочка командной строки на основе задач и язык сценариев, разработанные для администрирования системы. С помощью PowerShell можно легко контролировать и автоматизировать администрирование служб и приложений Azure. Например, с помощью командлетов можно выполнять те же задачи, которые доступны на [портале Azure](https://portal.azure.com).

В этом руководстве вы узнаете, как использовать [командлеты службы хранилища Azure](/powershell/module/azurerm.storage/#storage) для выполнения различных задач, связанных с разработкой и администрированием, в службе хранилища Azure.

Предполагается, что у вас есть опыт работы со [службой хранилища Azure](https://azure.microsoft.com/documentation/services/storage/) и [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Руководство предоставляет несколько сценариев для демонстрации использования PowerShell с службой хранилища Azure. Перед выполнением каждого сценария необходимо обновить переменные сценария в зависимости от конфигурации.

В первом разделе этого руководства содержится краткий обзор службы хранилища Azure и PowerShell. Для получения подробных сведений и инструкций начните с раздела [Предварительные требования для использования Azure PowerShell со службой хранилища Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).

## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Приступая к работе со службой хранилища Azure и PowerShell в течение 5 минут
В этом разделе показано, как получить доступ к хранилищу Azure через PowerShell в течение 5 минут.

**Впервые используете Azure?** Получите подписку на Microsoft Azure и связанную с ней учетную запись Microsoft. Сведения о способах приобретения Azure см. на страницах [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/), [Как приобрести Azure](https://azure.microsoft.com/pricing/purchase-options/) и [Предложения для участников](https://azure.microsoft.com/pricing/member-offers/) (для подписчиков MSDN, участников Microsoft Partner Network, BizSpark и других наших программ).

Дополнительные сведения о подписках Azure см. на странице [Назначение ролей администратора в Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**После создания подписки Microsoft Azure и учетной записи:**

1. [Скачайте и установите последнюю версию Azure PowerShell](https://github.com/Azure/azure-powershell/releases/latest).
2. Запустите интегрированную среду сценариев (ISE) Windows PowerShell. Для этого на локальном компьютере перейдите в меню **Пуск**. Введите **Администрирование** и щелкните, чтобы запустить оснастку. В окне **Администрирование** щелкните правой кнопкой мыши **Windows PowerShell ISE** и выберите **Запуск от имени администратора**.
3. В окне **Windows PowerShell ISE** щелкните **Файл** > **Создать**, чтобы создать новый файл скрипта.
4. Теперь мы предоставим вам простой сценарий, который показывает основные команды PowerShell для доступа к службе хранилища Azure. Сначала сценарий запросит учетные данные учетной записи Azure для ее добавления в локальной среде PowerShell. Затем сценарий установит подписку Azure по умолчанию и создаст новую учетную запись хранения в Azure. Затем сценарий создает новый контейнер в этой новой учетной записи хранения и передает существующий файл образа (BLOB) в этот контейнер. После этого сценарий выводит список всех BLOB-объектов в этом контейнере, создает новый каталог назначения на локальном компьютере и загружает файл образа.
5. В приведенном ниже разделе кода выберите скрипт между комментариями **#begin** и **#end**. Нажмите клавиши CTRL+C, чтобы скопировать его в буфер обмена.

    ```powershell
    # begin
    # Update with the name of your subscription.
    $SubscriptionName = "YourSubscriptionName"
       
    # Give a name to your new storage account. It must be lowercase!
    $StorageAccountName = "yourstorageaccountname"
       
    # Choose "West US" as an example.
    $Location = "West US"
       
    # Give a name to your new container.
    $ContainerName = "imagecontainer"
       
    # Have an image file and a source directory in your local computer.
    $ImageToUpload = "C:\Images\HelloWorld.png"
       
    # A destination directory in your local computer.
    $DestinationFolder = "C:\DownloadImages"
       
    # Add your Azure account to the local PowerShell environment.
    Add-AzureAccount
       
    # Set a default Azure subscription.
    Select-AzureSubscription -SubscriptionName $SubscriptionName –Default
       
    # Create a new storage account.
    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location
       
    # Set a default storage account.
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
       
    # Create a new container.
    New-AzureStorageContainer -Name $ContainerName -Permission Off
       
    # Upload a blob into a container.
    Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload
       
    # List all blobs in a container.
    Get-AzureStorageBlob -Container $ContainerName
       
    # Download blobs from the container:
    # Get a reference to a list of all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName
       
    # Create the destination directory.
    New-Item -Path $DestinationFolder -ItemType Directory -Force  
       
    # Download blobs into the local destination directory.
    $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
       
    # end
    ```

6. В окне **Интегрированная среда сценариев Windows PowerShell**нажмите сочетание клавиш CTRL+V, чтобы вставить сценарий. Щелкните **Файл** > **Сохранить**. В диалоговом окне **Сохранить как** введите имя файла сценария, например "mystoragescript". Щелкните **Сохранить**.
7. Теперь необходимо обновить переменные сценария на основе заданных параметров конфигурации. Необходимо обновить переменную **$SubscriptionName** данными собственной подписки. Можно сохранить значения других переменных, как указано в сценарии или обновлять их по своему усмотрению.
   
   * Необходимо обновить переменную **$SubscriptionName**, используя данные собственной подписки. Ниже представлены три способа найти название вашей подписки. Можно воспользоваться любым из них.
     
    а. В окне **Windows PowerShell ISE** щелкните **Файл** > **Создать**, чтобы создать новый файл скрипта. Скопируйте следующий скрипт в новый файл и выберите **Отладка** > **Выполнить**. Следующий сценарий сначала запросит учетные данные учетной записи Azure для добавления в учетной записи Azure в локальную среду PowerShell, и отобразит все подписки, которые подключены к локальному сеансу PowerShell. Запишите имя подписки, которая будет использоваться при выполнении этого учебника:
     
    ```powershell
    Add-AzureAccount 
      Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName
    ```

    b. Чтобы найти и скопировать имя подписки на [портале Azure](https://portal.azure.com), в расположенном слева главном меню щелкните элемент **Подписки**. Скопируйте имя подписки, которая будет использоваться при выполнении сценариев в этом руководстве.
     
     ![Портал Azure](./media/storage-powershell-guide-full/Subscription_Previewportal.png)

    c. Чтобы найти и скопировать имя подписки на [классическом портале Azure](https://manage.windowsazure.com/), выполните прокрутку вниз и выберите элемент **Параметры** в левой части портала. Щелкните элемент **Подписки** для просмотра списка подписок. Скопируйте имя подписки, которая будет использоваться при выполнении сценариев, указанных в этом руководстве.
     
     ![классическом портале Azure](./media/storage-powershell-guide-full/Subscription_currentportal.png)

   * **$StorageAccountName**: используйте заданное имя в скрипте или введите новое имя для вашей учетной записи хранения. **Важно!** Имя учетной записи хранения в Azure должно быть уникальным. и состоять из символов нижнего регистра.
   * **$Location**: используйте заданное в скрипте значение West US или выберите другое расположение Azure, например East US, North Europe и т. д.
   * **$ContainerName** : используйте заданное в сценарии имя или введите новое имя для своего контейнера.
   * **$ImageToUpload**: введите путь к изображению на локальном компьютере (например, C:\Images\HelloWorld.png).
   * **$DestinationFolder**: введите путь к локальному каталогу для хранения файлов, скачанных из службы хранилища Azure (например, C:\DownloadImages).
8. После обновления переменных скриптов в файле mystoragescript.ps1 щелкните **Файл** > **Сохранить**. Для выполнения скрипта щелкните **Отладка** > **Выполнить** или нажмите клавишу **F5**.  

После выполнения сценария появится локальная целевая папка, которая содержит загруженный файл образа. На следующем снимке экрана показан пример вывода:

![Загрузка BLOB-объектов](./media/storage-powershell-guide-full/Blobdownload.png)

> [!NOTE]
> В разделе «Приступая к работе со службой хранилища Azure и PowerShell в течение 5 минут» предоставляется краткое введение по использованию Azure PowerShell со службой хранилища Azure. С подробными сведениями и инструкциями мы рекомендуем вам ознакомиться в следующих разделах.
> 

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Предварительные требования для использования Azure PowerShell со службой хранилища Azure
Требуется подписка Azure и учетная запись для запуска командлетов PowerShell, приведенных в этом руководстве, как описано выше.

Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Сведения об установке и настройке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Рекомендуется загрузить и установить (или обновить до последней версии) модуль Azure PowerShell перед использованием в этом руководстве.

Вы можете запустить командлеты на стандартной консоли Windows PowerShell или в интегрированной среде сценариев Windows PowerShell (ISE). Например, чтобы открыть среду **Windows PowerShell ISE**, перейдите в меню "Пуск", введите "Администрирование" и щелкните, чтобы запустить оснастку. В окне «Администрирование» щелкните правой кнопкой мыши Windows PowerShell ISE и выберите «Запуск от имени администратора».

## <a name="how-to-manage-storage-accounts-in-azure"></a>Управление учетными записями хранения в Azure

Давайте рассмотрим процесс управления учетными записями хранения в Azure с помощью PowerShell.

### <a name="how-to-set-a-default-azure-subscription"></a>Как задать значение по умолчанию для подписки Azure
Для управления службой хранилища Azure с помощью Azure PowerShell необходимо выполнить проверку подлинности в клиентской среде с помощью проверки подлинности Azure Active Directory или проверки подлинности на основе сертификатов. Подробные сведения см. в руководстве [Установка и настройка Azure PowerShell](/powershell/azure/overview). В этом руководстве используется проверка подлинности Azure Active Directory.

1. В интегрированной среде сценариев Azure PowerShell введите следующую команду, чтобы добавить учетную запись Azure в локальную среду PowerShell:

    ```powershell
    Add-AzureAccount
    ```

2. В окне "Вход в Microsoft Azure" введите адрес электронной почты и пароль, связанные с вашей учетной записью. Azure выполняет проверку подлинности и сохраняет учетные данные, а затем закрывает окно.

3. Затем выполните следующую команду, чтобы просмотреть учетные записи Azure в локальной среде PowerShell и убедитесь, что ваша учетная запись имеется в списке:
   
    ```powershell
    Get-AzureAccount
    ```
4. Затем выполните следующий командлет, чтобы просмотреть все подписки, которые подключены к локальному сеансу PowerShell и убедитесь, что подписка есть в списке:

    ```powershell
    Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
    ```
5. Чтобы задать подписку Azure по умолчанию, выполните командлет Select-AzureSubscription.

    ```powershell
    $SubscriptionName = 'Your subscription Name'
    Select-AzureSubscription -SubscriptionName $SubscriptionName –Default
    ```

6. Проверьте имя подписки по умолчанию, выполнив командлет Get-AzureSubscription:

    ```powershell
    Get-AzureSubscription -Default
    ```

7. Чтобы просмотреть все доступные командлеты PowerShell для службы хранилища Azure, выполните следующую команду:
    
    ```powershell
    Get-Command -Module Azure -Noun *Storage*`
    ```

### <a name="how-to-create-a-new-azure-storage-account"></a>Создание новой учетной записи хранения Azure
Для использования службы хранилища Azure потребуется учетная запись хранения. После настройки компьютера для подключения к подписке можно создать новую учетную запись хранения Azure.

1. Выполните командлет Get-AzureLocation, чтобы найти все доступные расположения:

    ```powershell
    Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes
    ```

2. Затем выполните командлет New-AzureStorageAccount для создания новой учетной записи хранения. Этот пример создает новую учетную запись хранения в центре обработки данных «Запад США».
   
    ```powershell
    $location = "West US"
    $StorageAccountName = "yourstorageaccount"
    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location
    ```

> [!IMPORTANT]
> Имя для учетной записи хранения является уникальным в пределах Azure и должно содержать только строчные буквы. Соглашения об именовании и ограничениях см. в статьях [Об учетных записях хранения Azure](../storage-create-storage-account.md) и [Именование контейнеров, больших двоичных объектов, метаданных и ссылка на них](http://msdn.microsoft.com/library/azure/dd135715.aspx).
> 
> 

### <a name="how-to-set-a-default-azure-storage-account"></a>Как задать учетную запись хранения Azure по умолчанию
В подписке может иметься несколько учетных записей хранения. Можно выбрать одну из них и установить ее в качестве учетной записи хранения по умолчанию для всех команд хранилища в одном сеансе PowerShell. Это позволяет выполнять команды службы хранилища Azure PowerShell без явного указания контекста хранилища.

1. Чтобы задать учетную запись по умолчанию для подписки, можно запустить командлет Set-AzureSubscription.

    ```powershell
    $SubscriptionName = "Your subscription name"
    $StorageAccountName = "yourstorageaccount"  
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

2. Затем выполните командлет Get-AzureSubscription и убедитесь, что учетная запись хранения связана с вашей учетной записью в подписке по умолчанию. Эта команда возвращает свойства подписки для текущей подписки, включая ее текущую учетную запись хранения.

    ```powershell
    Get-AzureSubscription –Current
    ```

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Получение списка всех учетных записей хранения Azure в подписке
Каждая подписка Azure может включать до 100 учетных записей хранения. Актуальные сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md).

Выполните следующий командлет, чтобы узнать имена и состояние учетных записей хранения в текущей подписке.

```powershell
Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus
```

### <a name="how-to-create-an-azure-storage-context"></a>Создание контекста службы хранилища Azure
Контекст службы хранилища Azure — это объект в PowerShell для инкапсуляции учетных данных хранения. Использование контекста хранилища при запуске любого последующего командлета позволяет проводить проверку подлинности запроса без явного указания учетной записи хранения и ключа доступа. Контекст хранилища можно создать несколькими способами, например с помощью имени и ключа доступа учетной записи хранения, токена подписи общего доступа, строки подключения или анонимно. Дополнительные сведения см. в описании [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext).  

Используйте один из следующих трех способов создания контекста хранилища:

* Запустите командлет [Get-AzureStorageKey](/powershell/module/azure.storage/get-azurestoragekey) , чтобы найти основной ключ доступа к хранилищу для вашей учетной записи хранения Azure. Затем вызовите командлет [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) для создания контекста хранилища:

    ```powershell
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    ```

* Создайте токен подписи общего доступа для контейнера службы хранилища Azure и используйте его для создания контекста хранилища:

    ```powershell
    $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken
    ```

    Дополнительные сведения см. в статьях [New-AzureStorageContainerSASToken](/powershell/module/azure.storage/new-azurestoragecontainersastoken) и [Использование подписанных URL-адресов (SAS)](../storage-dotnet-shared-access-signature-part-1.md).

* В некоторых случаях может потребоваться указать конечные точки службы при создании нового контекста хранилища. Это может потребоваться при регистрации имени пользовательского домена для учетной записи службы BLOB-объектов или использовании подписанного URL-адреса для доступа к ресурсам хранилища. Установите конечные точки службы в строке соединения и используйте ее для создания нового контекста хранилища, как показано ниже:

    ```powershell
    $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString
    ```

Дополнительные сведения о том, как настроить строку подключения хранилища, см. [здесь](../storage-configure-connection-string.md).

Вы настроили компьютер и узнали, как управлять подписками и учетными записями хранения с помощью Azure PowerShell. Теперь переходите к следующему разделу, чтобы научиться управлять BLOB-объектами и моментальными снимками BLOB-объектов Azure.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Получение и повторное создание ключей к хранилищу данных Azure
Учетная запись хранилища Azure предоставляется с двумя ключами учетной записи. Для получения ключей можно использовать следующий командлет.

```powershell
Get-AzureStorageKey -StorageAccountName "yourstorageaccount"
```

Для получения конкретного ключа используйте следующий командлет. Допустимые значения: Primary и Secondary  

```powershell
(Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
    
(Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary
```

Для повторного создания ключей используйте следующий командлет. Допустимые значения параметра -KeyType: Primary и Secondary

```powershell
New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType "Primary"
    
New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType "Secondary"
```

## <a name="how-to-manage-azure-blobs"></a>Управление BLOB-объектами Azure
Хранилище BLOB-объектов Azure — это служба хранения большого количества неструктурированных данных, таких как текстовые или бинарные файлы, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. В этом разделе предполагается, что вы уже знакомы с понятиями службы хранилища BLOB-объектов. Дополнительные сведения см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../blobs/storage-dotnet-how-to-use-blobs.md) и [Понятия службы BLOB-объектов](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Создание контейнера
Каждый BLOB-объект в хранилище Azure должен находиться в контейнере. Можно создать закрытый контейнер, используя командлет New-AzureStorageContainer:

```powershell
$StorageContainerName = "yourcontainername"
New-AzureStorageContainer -Name $StorageContainerName -Permission Off
```

> [!NOTE]
> Существует три уровня анонимного доступа для чтения: **Отключено**, **Большой двоичный объект** и **Контейнер**. Чтобы запретить анонимный доступ к большому двоичному объекту, присвойте параметру разрешение **Отключено**. По умолчанию новый контейнер является закрытым, доступ к нему может осуществляться только владельцем учетной записи. Чтобы разрешить анонимный общий доступ на чтение к ресурсам больших двоичных объектов, но не к метаданным контейнера или списку больших двоичных объектов в контейнере, присвойте параметру разрешение **Большой двоичный объект**. Чтобы разрешить полный общий доступ на чтение к ресурсам больших двоичных объектов, метаданным контейнера и списку больших двоичных объектов в контейнере, присвойте параметру разрешение **Контейнер**. Дополнительные сведения см. в статье [Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам](../blobs/storage-manage-access-to-resources.md).
> 
> 

### <a name="how-to-upload-a-blob-into-a-container"></a>Передача BLOB-объекта в контейнер
Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты. Дополнительные сведения см. в статье [Understanding Block Blobs, Append Blobs, and Page Blobs](http://msdn.microsoft.com/library/azure/ee691964.aspx) (Основные сведения о блочных, добавочных и страничных BLOB-объектах).

Можно использовать командлет [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) , чтобы загрузить большие двоичные объекты в контейнер. По умолчанию эта команда отправляет локальные файлы в BLOB-объект. Чтобы указать тип BLOB-объекта можно использовать параметр -BlobType.

В следующем примере выполняется [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) для получения всех файлов в указанной папке и передачи их в следующий командлет с помощью оператора конвейера. Командлет [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) загружает локальные файлы в контейнер:

```powershell
Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"
```

### <a name="how-to-download-blobs-from-a-container"></a>Скачивание BLOB-объектов из контейнера
Следующий пример демонстрирует загрузку BLOB-объектов из контейнера. Сначала пример устанавливает соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее первичный ключ доступа. Затем в примере извлекается ссылка на большой двоичный объект с помощью командлета [Get AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) . Затем в примере используется командлет [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) , чтобы скачать большие двоичные объекты в локальную целевую папку.

```powershell
#Define the variables.
$ContainerName = "yourcontainername"
$DestinationFolder = "C:\DownloadImages"

#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#List all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

#Download blobs from a container.
New-Item -Path $DestinationFolder -ItemType Directory -Force
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx
```

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Копирование BLOB-объектов из одного контейнера хранилища в другой
Можно асинхронно копировать BLOB-объекты между учетными записями хранения и областями. Следующий пример демонстрирует копирование BLOB-объектов из одного контейнера хранилища в другой в двух разных учетных записях хранения. В примере сначала задаются переменные источника и назначения учетных записей хранения и затем создается контекст хранилища для каждой учетной записи. Далее в примере копируются большие двоичные объекты из контейнера-источника в целевой контейнер с помощью командлета [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) . В примере предполагается, что учетные записи хранения и контейнеры источника и назначения уже существуют.

```powershell
#Define the source storage account and context.
$SourceStorageAccountName = "yoursourcestorageaccount"
$SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
$SrcContainerName = "yoursrccontainername"
$SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

#Define the destination storage account and context.
$DestStorageAccountName = "yourdeststorageaccount"
$DestStorageAccountKey = "Storage key for yourdeststorageaccount"
$DestContainerName = "destcontainername"
$DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

#Get a reference to blobs in the source container.
$blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

#Copy blobs from one container to another.
$blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext
```

Обратите внимание, что этот пример выполняет асинхронное копирование. Отслеживать состояние каждой копии можно, запустив командлет [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/start-azurestorageblobcopystate) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Копирование больших двоичных объектов из дополнительного расположения
Большие двоичные объекты можно копировать из дополнительного расположения учетной записи с поддержкой RA-GRS.

```powershell
#define secondary storage context using a connection string constructed from secondary endpoints.
$SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext
```

### <a name="how-to-delete-a-blob"></a>Удаление BLOB-объекта
Чтобы удалить BLOB-объект, сначала нужно получить ссылку на него, а затем вызвать командлет Remove-AzureStorageBlob с этой ссылкой. Следующий пример удаляет все BLOB-объекты в данном контейнере. В примере сначала устанавливаются переменные для учетной записи хранения, затем создается контекст хранилища. Далее в примере извлекается ссылка на большой двоичный объект с помощью командлета [Get AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) и выполняется командлет [Remove-AzureStorageBlob](/powershell/module/azure.storage/remove-azurestorageblob), чтобы удалить большие двоичные объекты из контейнера в хранилище Azure.

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$ContainerName = "containername"
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#Get a reference to all the blobs in the container.
$blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

#Delete blobs in a specified container.
$blobs| Remove-AzureStorageBlob
```

## <a name="how-to-manage-azure-blob-snapshots"></a>Управление моментальными снимками BLOB-объектов Azure
Azure позволяет создать моментальный снимок BLOB-объекта. Моментальный снимок — это версия BLOB-объекта только для чтения, сделанная в определенный момент времени. После создания моментального снимка его можно читать, копировать или удалять, но нельзя изменять. Моментальные снимки обеспечивают способ резервного копирования BLOB-объекта в том виде, в котором он находится в данный момент времени. Дополнительные сведения см. в статье [Создание моментального снимка большого двоичного объекта](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Создание моментального снимка BLOB-объекта
Для создания моментального снимка большого двоичного объекта сначала нужно получить ссылку на него, а затем вызвать метод `ICloudBlob.CreateSnapshot` для этой ссылки. В следующем примере сначала устанавливаются переменные для учетной записи хранения и затем создается контекст хранилища. Далее в примере извлекается ссылка на большой двоичный объект с помощью командлета [Get AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) и выполняется метод [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) для создания моментального снимка.

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$ContainerName = "yourcontainername"
$BlobName = "yourblobname"
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#Get a reference to a blob.
$blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

#Create a snapshot of the blob.
$snap = $blob.ICloudBlob.CreateSnapshot()
```

### <a name="how-to-list-a-blobs-snapshots"></a>Получение списка моментальных снимков больших двоичных объектов
Можно создать любое количество моментальных снимков для BLOB-объекта. Можно вывести список моментальных снимков, связанных с BLOB-объектом, для отслеживания текущих моментальных снимков. В следующем примере используется стандартный большой двоичный объект и вызывается командлет [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) для вывода списка моментальных снимков этого объекта.  

```powershell
#Define the blob name.
$BlobName = "yourblobname"

#List the snapshots of a blob.
Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }
```

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Копирование моментального снимка BLOB-объекта
Для восстановления моментального снимка BLOB-объекта можно скопировать моментальный снимок. Подробные сведения и ограничения см. в статье [Создание моментального снимка большого двоичного объекта](http://msdn.microsoft.com/library/azure/hh488361.aspx). В следующем примере сначала устанавливаются переменные для учетной записи хранения и затем создается контекст хранилища. Далее в примере определяются переменные имен контейнера и BLOB-объекта. В примере извлекается ссылка на большой двоичный объект с помощью командлета [Get AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) и выполняется метод [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) для создания моментального снимка. Затем в примере выполняется командлет [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) для копирования моментального снимка большого двоичного объекта. При этом в качестве исходного большого двоичного объекта используется объект ICloudBlob. Не забудьте обновить переменные в зависимости от конфигурации до запуска примера. Обратите внимание, что в следующем примере предполагается, что контейнеры источника и назначения и BLOB-объект источника уже существуют.

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

#Define the variables.
$SrcContainerName = "yoursourcecontainername"
$DestContainerName = "yourdestcontainername"
$SrcBlobName = "yourblobname"
$DestBlobName = "CopyBlobName"

#Get a reference to a blob.
$blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

#Create a snapshot of a blob.
$snap = $blob.ICloudBlob.CreateSnapshot()

#Copy the snapshot to another container.
Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName
```

Вы узнали, как управлять BLOB-объектами и моментальными снимками BLOB-объектов Azure с помощью Azure PowerShell. Теперь переходите к следующему разделу, чтобы научиться управлять таблицами, очередями и файлами.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Управление таблицами и сущности таблицы Azure
Служба хранилища таблиц Azure — хранилище данных NoSQL, которое можно использовать для хранения и запросов огромных наборов структурированных нереляционных данных. Основными компонентами службы являются таблицы, сущности и свойства. Таблица представляет собой коллекцию сущностей. Сущность — это набор свойств. Каждая сущность может иметь до 252 свойств. Все они являются парами "имя — значение". В этом разделе предполагается, что вы уже знакомы с понятиями хранилища таблиц Azure. Дополнительные сведения см. в статьях [Understanding the Table Service Data Model](http://msdn.microsoft.com/library/azure/dd179338.aspx) (Общие сведения о модели данных службы таблиц) и [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

В следующих подразделах рассматривается управление службой хранилища таблиц Azure с помощью Azure PowerShell. Сценарии включают **создание**, **удаление** и **извлечение** **таблиц**, а также **добавление**, **запрос** и **удаление объектов таблиц**.

### <a name="how-to-create-a-table"></a>Создание таблицы
Каждая таблица должна находиться в учетной записи хранения Azure. Следующий пример демонстрирует создание таблицы в хранилище Azure. Сначала в примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем используется командлет [New-AzureStorageTable](/powershell/module/azure.storage/new-azurestoragetable) , чтобы создать таблицу в службе хранилища Azure.

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = "Storage key for yourstorageaccount ends with =="
$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

#Create a new table.
$tabName = "yourtablename"
New-AzureStorageTable –Name $tabName –Context $Ctx
```

### <a name="how-to-retrieve-a-table"></a>Получение таблицы
Можно запрашивать и получить одну или все таблицы в учетной записи хранения. Следующий пример демонстрирует извлечение указанной таблицы с помощью командлета [Get-AzureStorageTable](/powershell/module/azure.storage/get-azurestoragetable) .

```powershell
#Retrieve a table.
$tabName = "yourtablename"
Get-AzureStorageTable –Name $tabName –Context $Ctx
```

При вызове командлета Get-AzureStorageTable без параметров он получает все таблицы хранилища для учетной записи хранения.

### <a name="how-to-delete-a-table"></a>Удаление таблицы
Можно удалить таблицу из учетной записи хранения с помощью командлета [Remove-AzureStorageTable](/powershell/module/azure.storage/remove-azurestoragetable) .  

```powershell
#Delete a table.
$tabName = "yourtablename"
Remove-AzureStorageTable –Name $tabName –Context $Ctx
```

### <a name="how-to-manage-table-entities"></a>Управление сущностями таблицы
В настоящее время Azure PowerShell предоставляет командлеты для непосредственного управления сущностями таблицы. Для выполнения операций над сущностями в таблице можно использовать классы в [клиентской библиотеке службы хранилища Azure для .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Добавление сущностей таблицы
Чтобы добавить сущность в таблицу, необходимо сначала создать объект, который определяет свойства сущности. Сущность может иметь до 255 свойств, включая 3 системных свойства: **PartitionKey**, **RowKey** и **Timestamp**. Вы отвечаете за вставку и обновление значений **PartitionKey** и **RowKey**. Сервер управляет значением **Timestamp**, которое не может быть изменено. Вместе **PartitionKey** и **RowKey** однозначно идентифицируют каждую сущность в пределах таблицы.

* **PartitionKey**— определяет раздел, в котором хранится сущность.
* **RowKey**— уникально определяет сущность в разделе.

Можно определить до 252 свойств для сущности. Дополнительные сведения см. в статье [Understanding the Table Service Data Model](http://msdn.microsoft.com/library/azure/dd179338.aspx) (Общие сведения о модели данных службы таблиц).

Следующий пример демонстрирует добавление сущности в таблицу. В примере показано получение таблицы «employee» и добавление в нее нескольких сущностей. Во-первых, устанавливается соединение со службой хранилища Azure с помощью контекста учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем заданная таблица извлекается с помощью командлета [Get-AzureStorageTable](/powershell/module/azure.storage/get-azurestoragetable) . Если таблица не существует, то используется командлет [New-AzureStorageTable](/powershell/module/azure.storage/new-azurestoragetable) для создания таблицы в службе хранилища Azure. Далее в примере определяется пользовательская функция Add-Entity для добавления сущностей в таблицу путем указания раздела и ключа строки каждой сущности. Функция Add-Entity вызывает командлет [New-Object](http://technet.microsoft.com/library/hh849885.aspx) с классом [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) для создания объекта сущности. Позже в примере вызывается метод [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) этого объекта сущности для добавления в таблицу.

```powershell
#Function Add-Entity: Adds an employee entity to a table.
function Add-Entity() {
    [CmdletBinding()]
    param(
       $table,
       [String]$partitionKey,
       [String]$rowKey,
       [String]$name,
       [Int]$id
    )

  $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
  $entity.Properties.Add("Name", $name)
  $entity.Properties.Add("ID", $id)

  $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
}

#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
$TableName = "Employees"

#Retrieve the table if it already exists.
$table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

#Create a new table if it does not exist.
if ($table -eq $null)
{
   $table = New-AzureStorageTable –Name $TableName -Context $Ctx
}

#Add multiple entities to a table.
Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4
```

#### <a name="how-to-query-table-entities"></a>Запрос сущностей таблицы
Для запроса к таблице используется класс [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx). В следующем примере предполагается, что уже был запущен сценарий, приведенный в разделе о добавлении раздела сущностей данного руководства. Сначала пример устанавливает соединение со службой хранилища Azure, используя контекст хранилища, который включает имя учетной записи хранения и ее ключ доступа. Затем предпринимается попытка получить ранее созданную таблицу "Employees" с помощью командлета [Get-AzureStorageTable](/powershell/module/azure.storage/get-azurestoragetable). Вызов [New-Object](http://technet.microsoft.com/library/hh849885.aspx) в классе Microsoft.WindowsAzure.Storage.Table.TableQuery создает новый объект запроса. В примере выполняется поиск сущностей, которые содержат столбец "ID", значение которого равно 1, как указано в фильтре строк. Дополнительные сведения см. в статье [Querying Tables and Entities](http://msdn.microsoft.com/library/azure/dd894031.aspx) (Запросы к таблицам и сущностям). При выполнении этого запроса он возвращает все сущности, которые соответствуют условиям фильтра.

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
$TableName = "Employees"

#Get a reference to a table.
$table = Get-AzureStorageTable –Name $TableName -Context $Ctx

#Create a table query.
$query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

#Define columns to select.
$list = New-Object System.Collections.Generic.List[string]
$list.Add("RowKey")
$list.Add("ID")
$list.Add("Name")

#Set query details.
$query.FilterString = "ID gt 0"
$query.SelectColumns = $list
$query.TakeCount = 20

#Execute the query.
$entities = $table.CloudTable.ExecuteQuery($query)

#Display entity properties with the table format.
$entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize
```

#### <a name="how-to-delete-table-entities"></a>Удаление сущностей таблицы
Сущность можно удалить с помощью ее ключей раздела и строки. В следующем примере предполагается, что уже был запущен сценарий, приведенный в разделе о добавлении раздела сущностей данного руководства. Сначала пример устанавливает соединение со службой хранилища Azure, используя контекст хранилища, который включает имя учетной записи хранения и ее ключ доступа. Затем предпринимается попытка получить ранее созданную таблицу "Employees" с помощью командлета [Get-AzureStorageTable](/powershell/module/azure.storage/get-azurestoragetable). Если таблица существует, в примере вызывается метод [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) для получения сущности, основываясь на значениях ключа раздела и строки. Затем передайте сущность в метод [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) для удаления.

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

#Retrieve the table.
$TableName = "Employees"
$table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

#If the table exists, start deleting its entities.
if ($table -ne $null) 
{
    #Together the PartitionKey and RowKey uniquely identify every  
    #entity within a table.
    $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
    $entity = $tableResult.Result
    if ($entity -ne $null)
    {
        #Delete the entity.
        $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
}
```

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Управление очередями Azure и очередями сообщений
Хранилище очередей Azure — это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. В этом разделе предполагается, что вы уже знакомы с понятиями службы хранилища очередей Azure. Дополнительные сведения см. в статье [Приступая к работе с хранилищем очередей Azure с помощью .NET](../storage-dotnet-how-to-use-queues.md).

В этом разделе показано, как управлять службой хранилища очередей Azure с помощью Azure PowerShell. Сценарии использования включают **вставку** и **удаление** сообщений очереди, а также **создание**, **удаление** и **получение очередей**.

### <a name="how-to-create-a-queue"></a>Как создать очередь
Сначала в этом примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем вызывается командлет [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue), чтобы создать очередь с именем "queuename".

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
$QueueName = "queuename"
$Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx
```

Сведения о соглашениях об именовании для службы очередей Azure см. в статье [Именование очередей и метаданных](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Получение очереди
Можно запрашивать и получать указанную очередь или список всех очередей в учетной записи хранения. Следующий пример демонстрирует извлечение указанной очереди с помощью командлета [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) .

```powershell
#Retrieve a queue.
$QueueName = "queuename"
$Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx
```

При вызове командлета [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) без параметров возвращается список всех очередей.

### <a name="how-to-delete-a-queue"></a>Удаление очереди
Для удаления очереди и всех сообщений, содержащихся в ней, вызовите командлет Remove-AzureStorageQueue. В следующем примере показан способ удаления указанной очереди, используя командлет Remove-AzureStorageQueue.

```powershell
#Delete a queue.
$QueueName = "yourqueuename"
Remove-AzureStorageQueue –Name $QueueName –Context $Ctx
```

#### <a name="how-to-insert-a-message-into-a-queue"></a>Вставка сообщения в очередь
Чтобы вставить сообщение в существующую очередь, сначала необходимо создать новый экземпляр класса [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Затем вызовите метод [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Для создания CloudQueueMessage можно использовать строку (в формате UTF-8) или массив байтов.

Следующий пример демонстрирует добавление сообщений в очередь. Сначала в примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем извлекается указанная очередь с помощью командлета [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Если очередь существует, командлет [New-Object](http://technet.microsoft.com/library/hh849885.aspx) используется для создания экземпляра класса [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Позже в примере вызывается метод [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) для этого объекта сообщения, чтобы добавить его в очередь. Ниже приведен код, который извлекает очередь и вставляет сообщение "MessageInfo".

```powershell
#Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

#Retrieve the queue.
$QueueName = "queuename"
$Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

#If the queue exists, add a new message.
if ($Queue -ne $null) {
   # Create a new message using a constructor of the CloudQueueMessage class.
   $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

   # Add a new message to the queue.
   $Queue.CloudQueue.AddMessage($QueueMessage)
}
```

#### <a name="how-to-de-queue-at-the-next-message"></a>Удаление следующего сообщения из очереди
Код удаляет сообщение из очереди в два этапа. При вызове метода [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessage** , становится невидимым для другого кода, считывающего сообщения из этой очереди. Чтобы завершить удаление сообщения из очереди, необходимо вызвать метод [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **DeleteMessage** сразу после обработки сообщения.

```powershell
# Define the storage account and context.
$StorageAccountName = "yourstorageaccount"
$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
$Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

# Retrieve the queue.
$QueueName = "queuename"
$Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

$InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Get the message object from the queue.
$QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
# Delete the message.
$Queue.CloudQueue.DeleteMessage($QueueMessage)
```

## <a name="how-to-manage-azure-file-shares-and-files"></a>Управление общими папками и файлами Azure
Хранилище файлов Azure предоставляет общее хранилище для приложений с помощью стандартного протокола SMB. Виртуальные машины Microsoft Azure и облачные службы могут совместно использовать файл данных между компонентами приложения через подключенные общие ресурсы, локальные приложения могут обращаться к данным файлов в общей папке через API хранилища файлов или Azure PowerShell.

Подробнее о хранилище файлов Azure см. в статьях [Приступая к работе с хранилищем файлов Azure в Windows](../storage-dotnet-how-to-use-files.md) и [API REST службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Установка и запросы к аналитике хранилища
Можно воспользоваться [аналитикой службы хранилища Azure](../storage-analytics.md) для сбора метрик из учетных записей хранения Azure и ведения журналов с данными о запросах, отправленных в вашу учетную запись хранения. Метрики хранилища можно использовать для наблюдения за работоспособностью учетной записи хранения и ведения журнала хранилища для диагностики и устранения проблем, связанных с вашей учетной записью хранения. Вы можете настроить мониторинг с помощью портала Azure или Windows PowerShell или программно, с помощью клиентской библиотеки хранилища. Ведение журналов хранилища производится на стороне сервера и позволяет записывать сведения об успешных и неудачных запросах в вашей учетной записи хранения. Эти журналы позволяют просмотреть подробные сведения об операциях чтения, записи и удаления для таблиц, очередей и BLOB-объектов, а также причины неудачных запросов.

Сведения о том, как включить и просмотреть метрики хранилища данных с помощью PowerShell, представлены в статье [Включение метрик хранилища с помощью PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Сведения о том, как включить и получить журнал хранилища данных с помощью PowerShell, см. в статьях [How to enable Storage Logging using PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) (Включение ведения журнала хранилища с помощью PowerShell) и [Finding your Storage Logging log data](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata) (Поиск данных журнала хранилища).
Подробнее об использовании метрик хранилища и ведения журнала для устранения неполадок хранилища см. в статье [Мониторинг, диагностика и устранение неполадок службы хранилища Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Как управлять подписанными URL-адресами (SAS) и хранимыми политиками доступа
Подписанные URL-адреса являются важной частью модели обеспечения безопасности для любого приложения, использующего хранилище Azure. Их удобно применять в целях предоставления ограниченных разрешений для вашей учетной записи хранения тем клиентам, которые нельзя передавать ключ учетной записи. По умолчанию только владелец учетной записи хранения может обращаться к большим двоичным объектам, таблицам и очередям в пределах этой учетной записи. Если вашей службе или приложению нужно сделать эти ресурсы доступными другим клиентам без совместного использования ключа доступа, вы можете воспользоваться следующими тремя вариантами:

* Задайте такие разрешения контейнера, которые предоставляют анонимный доступ для чтения контейнера и его больших двоичных объектов. Такие разрешения нельзя задать для таблиц и очередей.
* Используйте подписанный URL-адрес, обеспечивающий ограниченные права доступа к контейнерам, большим двоичным объектам, очередям и таблицам в заданный интервал времени.
* Используйте хранимую политику доступа, чтобы получить дополнительный уровень контроля над подписанными URL-адресами для контейнера, его больших двоичных объектов, очереди или таблицы. С помощью хранимой политики доступа можно изменить время начала, срок действия и разрешения для подписи, а также отозвать подпись после ее выдачи.

Подписанный URL-адрес может быть в одной из двух следующих форм.

* **Нерегламентированная SAS:**при создании нерегламентированной SAS время начала, время окончания срока действия и разрешения для SAS указываются в универсальном коде ресурса (URI) SAS. Этот тип подписанного URL-адреса можно создать для контейнера, большого двоичного объекта, таблицы или очереди, и его невозможно отозвать.
* **SAS с хранимой политикой доступа:** хранимая политика доступа определяется в контейнере ресурсов, контейнере больших двоичных объектов, таблице или очереди. Ее можно использовать для управления ограничениями одного или нескольких подписанных URL-адресов. При сопоставлении подписи общего доступа с хранимой политикой доступа эта подпись наследует ограничения (время начала, время окончания и разрешения), определенные для данной хранимой политики доступа. Подписанный URL-адрес этого типа можно отозвать.

Дополнительные сведения см. в статьях [Использование подписанных URL-адресов (SAS)](../storage-dotnet-shared-access-signature-part-1.md) и [Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам](../blobs/storage-manage-access-to-resources.md).

В следующих разделах вы узнаете, как создать маркер подписанного URL-адреса и хранимую политику доступа для таблиц Azure. Azure PowerShell предоставляет одинаковые командлеты для контейнеров, больших двоичных объектов и очередей. Для выполнения сценариев в этом разделе скачайте [Azure PowerShell версии 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) или более поздней.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Как создать маркер подписанного URL-адреса на основе политики
Используйте командлет New-AzureStorageTableStoredAccessPolicy для создания новой хранимой политики доступа. Затем вызовите командлет [New-AzureStorageTableSASToken](/powershell/module/azure.storage/new-azurestoragetablesastoken) , чтобы создать новый токен подписанного URL-адреса на основе политики для таблицы службы хранилища Azure.

```powershell
$policy = "policy1"
New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx
```

### <a name="how-to-create-an-ad-hoc-non-revocable-shared-access-signature-token"></a>Как создать маркер однорангового (неотзываемого) подписанного URL-адреса
Воспользуйтесь командлетом [New-AzureStorageTableSASToken](/powershell/module/azure.storage/new-azurestoragetablesastoken), чтобы создать новый специализированный (неотзываемый) маркер подписанного URL-адреса для таблицы службы хранилища Azure.

```powershell
New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx
```
    
### <a name="how-to-create-a-stored-access-policy"></a>Как создать хранимую политику доступа
Используйте командлет New-AzureStorageTableStoredAccessPolicy для создания новой хранимой политики доступа для таблицы хранения Azure.

```powershell
$policy = "policy1"
New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
```
    
### <a name="how-to-update-a-stored-access-policy"></a>Как обновить хранимую политику доступа
Используйте командлет Set-AzureStorageTableStoredAccessPolicy для обновления существующей хранимой политики доступа для таблицы хранения Azure.

```powershell
Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx
```

### <a name="how-to-delete-a-stored-access-policy"></a>Как удалить хранимую политику доступа
Используйте командлет Remove-AzureStorageTableStoredAccessPolicy для удаления хранимой политики доступа в таблице хранения Azure.

```powershell
Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx
```

## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Использование службы хранилища Azure для правительства США и Azure в Китае
Среда Azure — это независимое развертывание Microsoft Azure, такое как [Azure для государственных организаций в правительстве США](https://azure.microsoft.com/features/gov/), [AzureCloud для глобального Azure](https://portal.azure.com) и [AzureChinaCloud для Azure под управлением 21Vianet в Китае](http://www.windowsazure.cn/). Можно выполнить развертывание новых сред Azure для правительства США и Китая.

Для использования службы хранилища Azure с AzureChinaCloud необходимо создать контекст хранилища, связанный с AzureChinaCloud. Выполните следующие действия, чтобы приступить к работе.

1. Запустите командлет [Get-AzureEnvironment](/powershell/module/azure/get-azureenvironment?view=azuresmps-3.7.0) , чтобы просмотреть доступные среды Azure:
   
    ```powershell
    Get-AzureEnvironment
    ```

2. Добавьте учетную запись Azure Китай в Windows PowerShell:
   
    ```powershell
    Add-AzureAccount –Environment AzureChinaCloud
    ```

3. Создаете контекст хранилища для учетной записи AzureChinaCloud:
   
    ```powershell
    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud
    ```

Для использования службы хранилища Azure с [Azure для правительства США ](https://azure.microsoft.com/features/gov/)следует определить новую среду и затем создать новый контекст хранилища с данной средой:

1. Запустите командлет [Get-AzureEnvironment](/powershell/module/azure/get-azureenvironment?view=azuresmps-3.7.0) , чтобы просмотреть доступные среды Azure:

    ```powershell
    Get-AzureEnvironment
    ```

2. Добавьте учетную запись Azure для правительства США (AzureUSGovernment) в Windows PowerShell:
   
    ```powershell
    Add-AzureAccount –Environment AzureUSGovernment
    ```

3. Создайте контекст хранилища для учетной записи AzureUSGovernment:
   
    ```powershell
    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment
    ```
     
Дополнительные сведения можно найти в разделе 

* [Руководство для разработчиков Microsoft Azure для государственных организаций](../../azure-government/documentation-government-developer-guide.md)
* [Обзор отличий при создании приложения в службе в Китае](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы узнали, как управлять службой хранилища Azure с помощью Azure PowerShell. Ниже приведены некоторые связанные статьи и ресурсы для их изучения.

* [Документация по хранилищу Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Командлеты PowerShell службы хранилища Azure](/powershell/module/azurerm.storage/#storage)
* [Справочник по Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next

