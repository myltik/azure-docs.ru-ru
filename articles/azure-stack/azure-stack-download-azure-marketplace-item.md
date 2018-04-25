---
title: Скачивание элементов Marketplace из Azure | Документация Майкрософт
description: Скачивание элементов Marketplace из Azure в развертывание Azure Stack.
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
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c7d28831edf028657b7dd2a6a404a692a4a3bcb0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Скачивание элементов Marketplace из Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*


Когда вы решаете, какое содержимое включить в ваш Azure Stack Marketplace, нужно рассмотреть содержимое, доступное в Azure Stack Marketplace. Можно скачать проверенный список элементов Azure Marketplace, который был заранее протестирован для выполнения в Azure Stack. В этот список часто добавляются новые элементы, поэтому его следует периодически проверять.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Скачивание элементов Marketplace в подключенном режиме (с подключением к Интернету)

1. Чтобы скачать элементы Marketplace, сначала необходимо [зарегистрироваться в Azure Stack с помощью Azure](azure-stack-register.md).
2. Войдите на портал администратора Azure Stack (https://portal.local.azurestack.external).
3. Некоторые элементы Marketplace могут быть объемными. Убедитесь, что в системе есть достаточно места, щелкнув **Поставщики ресурсов** > **Хранилище**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Щелкните **Больше служб** > **Marketplace Management** (Управление Marketplace).

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Щелкните **Add from Azure** (Добавить из Azure), чтобы просмотреть список доступных для скачивания элементов. Можно щелкнуть каждый элемент в списке, чтобы просмотреть его описание и размер скачиваемого файла.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Выберите нужный элемент из списка и щелкните **Скачать**. Начнется скачивание образа виртуальной машины для выбранного элемента. Время скачивания может различаться.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. После завершения скачивания можно развернуть новый элемент Marketplace от имени оператора или пользователя Azure Stack. Щелкните **+Создать**, выберите новый элемент Marketplace, выполнив поиск в категориях.
7. Щелкните **Создать**, чтобы начать процесс создания для скачанного элемента. Выполните пошаговые инструкции для развертывания элемента.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Скачивание элементов Marketplace в отключенном или частично подключенном режиме (с ограниченным подключением к Интернету)

При развертывании Azure Stack в отключенном режиме (без подключения к Интернету) вы не сможете скачать элементы Marketplace с помощью портала Azure Stack. Но можно воспользоваться средством синдикации Marketplace, чтобы скачать элементы Marketplace на компьютер с подключением к Интернету, а затем передать их в среду Azure Stack.

### <a name="prerequisites"></a>предварительным требованиям
Прежде чем использовать средство синдикации Marketplace, [зарегистрируйте Azure Stack в подписке Azure](azure-stack-register.md).  

Чтобы скачать необходимые элементы Marketplace, выполните следующие действия на компьютере с подключением к Интернету.

1. Откройте консоль PowerShell от имени администратора и [установите модули PowerShell для Azure Stack](azure-stack-powershell-install.md). Необходимо установить **PowerShell версии 1.2.11 или более поздней**.  

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, выполните командлет **Connect-AzureRmAccount** без параметров. Вам будет предложено ввести учетные данные учетной записи Azure. Также может потребоваться выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.  

3. Если у вас несколько подписок, выполните следующую команду, чтобы выбрать подписку, которая использовалась для регистрации:  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Скачайте последнюю версию средства синдикации Marketplace с помощью следующего скрипта:  

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
   cd \AzureStack-Tools-master

   ```

5. Импортируйте модуль синдикации и запустите средство, выполнив следующие команды:  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. При запуске средства появится запрос на ввод учетных данных Azure. Войдите в учетную запись Azure, которая использовалась для регистрации Azure Stack. Если вход выполнен успешно, появится следующий экран со списком доступных элементов Marketplace.  

   ![Всплывающее окно с элементами Azure Marketplace](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Выберите образ, который необходимо скачать, и запишите его версию. Вы можете выбрать несколько образов, удерживая нажатой клавишу CTRL. Эта версия понадобится при импорте образа в следующем разделе.  Затем щелкните **ОК** и примите условия использования, щелкнув **Да**. Кроме того, вы можете отфильтровать список образов с помощью параметра **Добавить условия**. 

   Скачивание занимает некоторое время в зависимости от размера образа. После скачивания образа он будет доступен в пути назначения, который вы указали ранее. Скачанный пакет содержит VHD-файл и элементы коллекции в формате AZPKG.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Импорт образа и его публикация в Azure Stack Marketplace

1. Скачав пакет с образом и коллекцией, сохраните его с содержимым в папке AzureStack-Tools-master на съемном диске и скопируйте эту папку в среду Azure Stack (ее можно скопировать в любое локальное расположение, например C:\MarketplaceImages).     

2. Прежде чем импортировать образ, нужно подключиться к среде оператора Azure Stack, выполнив действия, описанные в статье [Настройка среды PowerShell оператора Azure Stack](azure-stack-powershell-configure-admin.md).  

3. Импортируйте образ в Azure Stack с помощью командлета Add-AzsVMImage. При использовании этого командлета замените значения *publisher*, *offer* и других параметров значениями для импортируемого образа. Значения параметров *publisher*, *offer* и *sku* для образа можно получить из объекта imageReference в скачанном ранее AZPKG-файле, а значение параметра *version* — на шаге 6 в предыдущем разделе.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Замените значения параметров и выполните следующую команду:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. Используйте портал, чтобы импортировать элемент Marketplace (AZPKG) в хранилище BLOB-объектов Azure Stack. Вы можете использовать локальное хранилище Azure Stack или службу хранилища Azure. (Это временное расположение для файла пакета.) Убедитесь, что используемый большой двоичный объект является общедоступным и запишите его URI.  

5. Опубликуйте элемент Marketplace в Azure Stack с помощью командлета **Add-AzsGalleryItem**. Например: 

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. Когда элемент коллекции будет опубликован, вы можете просмотреть его, выбрав **Создать** > **Marketplace**.  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Дополнительная информация

[Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)
