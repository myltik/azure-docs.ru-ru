---
title: Скачивание элементов Marketplace из Azure | Документация Майкрософт
description: Оператор облака может скачивать элементы marketplace из Azure в свое развертывание Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 69148a0ac9a5761eeee0ab47d83862724583619a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258799"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Скачивание элементов Marketplace из Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы, как оператор облака, можете скачивать элементы из Azure Marketplace и делать их доступными в Azure Stack. Элементы, которые вы можете выбрать, находятся в рекомендуемом списке элементов Azure Marketplace, которые предварительно протестированы и поддерживаются в Azure Stack. В этот список часто добавляются дополнительные элементы, поэтому его следует периодически проверять. 

Существует два сценария для подключения к Azure Marketplace. 

- **Сценарий с подключением** — это сценарий, в котором требуется, чтобы ваша среда Azure Stack была подключена к Интернету. Для поиска и скачивания элементов используйте портал Azure Stack. 
- **Сценарий без подключения или с частичным подключением**. В этом сценарии требуется доступ к Интернету с использованием средства синдикации marketplace для скачивания элементов marketplace. Затем вы переносите скачанные элементы в свою отключенную установку Azure Stack. В этом сценарии используется PowerShell.

Список элементов marketplace, которые можно скачать, см. в статье [Элементы Azure Marketplace, доступные для Azure Stack](azure-stack-marketplace-azure-items.md).


## <a name="connected-scenario"></a>Сценарий с подключением
Если у Azure Stack есть подключение к Интернету, для скачивания элементов marketplace можно использовать портал администратора.

### <a name="prerequisites"></a>предварительным требованиям
Экземпляр развертывания Azure Stack должен быть подключен к Интернету и зарегистрирован [в Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Использование портала для загрузки элементов из marketplace  
1. Войдите на портал администратора Azure Stack.

2.  Проверьте объем свободного места перед скачиванием элементов из marketplace. Позже, когда вы будете выбирать элементы для скачивания, вы можете сравнить размер скачиваемых элементов с доступной емкостью хранилища. Если емкость ограничена, рассмотрите варианты [управления доступным пространством](azure-stack-manage-storage-shares.md#manage-available-space). 

    Чтобы просмотреть доступное пространство, в разделе **управления регионами** выберите регион, который вы хотите изучить, а затем щелкните **Поставщики ресурсов** > **Хранилище**.

    ![Проверка дискового пространства](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Откройте Azure Stack Marketplace и подключитесь к Azure. Для этого выберите **Marketplace management** (Управление Marketplace), а затем щелкните **Add from Azure** (Добавить из Azure).

    ![Добавление из Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    На портале отображается список элементов, доступных для скачивания из Azure Marketplace. Вы можете щелкнуть каждый элемент, чтобы просмотреть его описание и дополнительную информацию о нем, включая размер его загрузки. 

    ![Список в Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Выберите нужный элемент из списка и щелкните **Скачать**. Время скачивания может различаться.

    ![Сообщение о загрузке](media/azure-stack-download-azure-marketplace-item/image04.png)

    После завершения скачивания можно развернуть новый элемент marketplace от имени оператора или пользователя Azure Stack.

5. Чтобы развернуть скачанный элемент, щелкните **+Создать**, выберите новый элемент, выполнив поиск в категориях. Выберите элемент, чтобы начать процесс развертывания. Процесс различается для разных элементов marketplace. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Сценарий отключенного или частично подключенного режима

При развертывании Azure Stack в отключенном режиме используйте PowerShell и *средство синдикации marketplace*, чтобы скачать элементы marketplace на компьютер с подключением к Интернету. Затем их можно передать в среду Azure Stack. В отключенной среде вы не можете скачивать элементы marketplace, используя портал Azure Stack. 

Средство синдикации marketplace может также использоваться в подключенном сценарии. 

Этот сценарий состоит из двух частей:
- **Часть 1**. Скачивание из Azure Marketplace. На компьютере с доступом к Интернету настройте PowerShell, скачайте средство синдикации, а затем скачайте элементы из Azure Marketplace.  
- **Часть 2**. Загрузка и публикация в Azure Stack Marketplace. Переместите файлы, скачанные в среду Azure Stack, импортируйте их в Azure Stack, а затем опубликуйте их в Azure Stack Marketplace.  


### <a name="prerequisites"></a>предварительным требованиям
- Развертывание Azure Stack должно быть [зарегистрировано в Azure](azure-stack-register.md).  

- На компьютере с подключением к Интернету необходимо установить модуль **PowerShell для Azure Stack версии 1.2.11** или более поздней. При необходимости см. [инструкции по установке модулей PowerShell для Azure Stack](azure-stack-powershell-install.md).  

- Чтобы включить импорт скачанных элементов marketplace, необходимо настроить среду [​​PowerShell для оператора Azure Stack](azure-stack-powershell-configure-admin.md).  

- У вас должна быть учетная запись хранения в Azure Stack, которая имеет общедоступный контейнер (в виде большого двоичного объекта хранилища). Используйте контейнер в качестве временного хранилища для файлов коллекции элементов marketplace. Если вы не знакомы с учетными записями хранения и контейнерами, см. статью [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) в документации Azure.

- Средство синдикации marketplace скачивается во время первой процедуры. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Скачивание элементов marketplace с помощью средства синдикации marketplace

1. На компьютере с подключением к Интернету откройте консоль PowerShell в качестве администратора.

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, в PowerShell запустите `Add-AzureRmAccount` без каких-либо параметров. Вам будет предложено ввести учетные данные учетной записи Azure. Также может потребоваться выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

3. Если у вас несколько подписок, выполните следующую команду, чтобы выбрать подписку, которая использовалась для регистрации:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Скачайте последнюю версию средства синдикации marketplace с помощью следующего скрипта:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Импортируйте модуль синдикации и запустите средство, выполнив следующий скрипт. Замените *Destination folder path* на расположение для хранения файлов, скачанных из Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. При запуске средства появится запрос на ввод учетных данных Azure. Войдите в учетную запись Azure, которая использовалась для регистрации Azure Stack. Если вход выполнен успешно, появится следующий экран со списком доступных элементов marketplace.  

   ![Всплывающее окно с элементами Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Выберите элемент, который необходимо скачать, и запишите его *версию*. Вы можете выбрать несколько образов, удерживая нажатой клавишу *CTRL*. Вы будете ссылаться на *версию* при импорте элемента в следующей процедуре. 
   
   Кроме того, вы можете отфильтровать список образов с помощью параметра **Добавить условия**.

8. Нажмите кнопку **ОК**, прочтите и примите юридические условия. 

9. Время скачивания зависит от размера элемента. После завершения скачивания элемент доступен в папке, указанной в скрипте. Скачанный пакет содержит VHD-файл (для виртуальных машин) или ZIP-файл (для расширений виртуальных машин). Он также содержит пакет коллекции в формате *AZPKG*. (Пакет *AZPKG* — это *ZIP*-файл.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Импорт скачанного пакета и его публикация в Azure Stack Marketplace
1. Файлы образов виртуальных машин или шаблонов решений, которые вы [скачали ранее](#use-the-marketplace-syndication-tool-to-download-marketplace-items), должны быть локально доступны для вашей среды Azure Stack.  

2. Импортируйте VHD-файлы в Azure Stack. Чтобы успешно импортировать образы виртуальных машин, вы должны иметь следующие сведения о виртуальной машине:
   - *Версия*, как указано на шаге 7 предыдущей процедуры.
   - Значения параметров *publisher*, *offer* и *sku*. Чтобы получить эти значения, переименуйте копию файла **AZPKG**, изменив расширение файла на **.zip**. Затем вы можете использовать текстовый редактор, чтобы открыть **DeploymentTemplates\CreateUiDefinition.json**. В файле JSON найдите раздел *imageReference*, который содержит эти значения элементов marketplace. В следующем примере показано, как появляются эти сведения:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Импортируйте образ в Azure Stack с помощью командлета **Add-AzsPlatformimage**. При использовании этого командлета замените значения *publisher*, *offer* и других параметров значениями для импортируемого образа. Вы можете получить значения *publisher*, *offer* и *sku* образа из текстового файла, который скачивается вместе с файлом AZPKG и сохраняется в месте назначения. 

   В следующем примере скрипта используются значения для виртуальной машины Windows Server 2016 Datacenter Server Core. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **О шаблонах решений**. Некоторые шаблоны могут содержать небольшой VHD-файл размером 3 МБ с именем **fixed3.vhd**. Вам не нужно импортировать этот файл в Azure Stack. Fixed3.vhd.  Этот файл входит в состав некоторых шаблонов решений, чтобы были выполнены требования к публикации в Azure Marketplace.

   Просмотрите описание шаблонов, скачайте, а затем импортируйте дополнительные компоненты, например виртуальные жесткие диски, которые требуются для работы с шаблоном решения.

3. Используя портал администратора, отправьте пакет элементов marketplace (файл AZPKG) в хранилище BLOB-объектов Azure Stack. Загрузка пакета делает его доступным для Azure Stack, поэтому вы сможете позже опубликовать элемент в Azure Stack Marketplace.

   Для загрузки требуется наличие учетной записи хранения с общедоступным контейнером (см. предварительные условия для этого сценария).   
   1. На портале администрирования Azure Stack выберите **Больше служб** > **Учетные записи хранения**.  
   
   2. Выберите учетную запись хранения из своей подписки, а затем в колонке **службы BLOB-объектов** выберите **Контейнеры**.  
      ![Служба BLOB-объектов](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Выберите контейнер, который вы хотите использовать, а затем нажмите кнопку **Отправить**, чтобы открыть панель **Отправить BLOB-объект**.  
      ![Контейнер](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. На панели "Отправить BLOB-объект" перейдите к файлам, которые вы хотите загрузить в хранилище, а затем нажмите кнопку **Отправить**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Переданные файлы отображаются в области контейнера. Выберите файл, а затем скопируйте URL-адрес из панели **Свойства BLOB-объекта**. Вы будете использовать этот URL-адрес на следующем шаге при импорте элементов marketplace в Azure Stack.  На следующем изображении контейнером является *blob-test-storage*, а файлом — *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  URL-адрес файла — *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Свойства большого двоичного объекта](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Используя PowerShell, опубликуйте элемент marketplace в Azure Stack с помощью командлета **Add-AzsGalleryItem**. Например:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. После публикации элемента коллекции его можно просмотреть, щелкнув **Больше служб** > **Marketplace**.  Если вы загружаете шаблон решения, обязательно добавьте любой зависимый образ VHD для этого шаблона решения.  
  ![Представление в Marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> В выпуске Azure Stack PowerShell 1.3.0 вы можете добавлять расширения виртуальной машины.

Например: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Дополнительная информация
[Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)