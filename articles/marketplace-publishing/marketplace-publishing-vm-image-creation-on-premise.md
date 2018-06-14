---
title: Локальное создание образа виртуальной машины для Azure Marketplace | Документация Майкрософт
description: Узнайте и выполните действия по созданию локального образа виртуальной машины, а затем разверните его в Azure Marketplace и сделайте доступным для покупки.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: 6b927ce6032092ce258eeebca49da0571439dbfb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29944437"
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Локальная разработка образа виртуальной машины для Azure Marketplace
Настоятельно рекомендуется разрабатывать виртуальные жесткие диски (VHD) Azure прямо в облаке, используя для этого протокол удаленного рабочего стола. Однако при необходимости вы можете загрузить VHD и выполнить разработку в локальной инфраструктуре.  

Для локальной разработки необходимо загрузить VHD ОС созданной виртуальной машины. Эти действия входят в шаг 3.3, описанный ранее.  

## <a name="download-a-vhd-image"></a>Загрузка образа VHD
### <a name="locate-a-blob-url"></a>Поиск URL-адреса BLOB-объектов
Чтобы загрузить VHD, найдите URL-адрес большого двоичного объекта диска ОС.

Найдите URL-адрес большого двоичного объекта на новом [портале Microsoft Azure](https://portal.azure.com):

1. Последовательно выберите пункты **Обзор** > **Виртуальные машины** и щелкните развернутую виртуальную машину.
2. В разделе **Настройка** выберите плитку **Диски**. Откроется колонка "Диски".
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Выберите **Диск ОС**. Откроется другая колонка со свойствами диска, включая расположение VHD.
4. Скопируйте этот URL-адрес большого двоичного объекта.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Теперь удалите развернутую виртуальную машину, не удаляя резервные копии дисков. Вместо удаления виртуальную машину можно также остановить. Не загружайте VHD ОС, пока виртуальная машина работает.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Загрузка VHD
Если URL-адрес большого двоичного объекта известен, вы можете загрузить VHD с помощью [портала Azure](http://manage.windowsazure.com/) или PowerShell.  

> [!NOTE]
> На момент составления данного руководства новый портал Microsoft Azure еще не содержит функцию загрузки VHD.  
> 
> 

**Загрузка VHD ОС через действующий [портал управления Azure](http://manage.windowsazure.com/)**

1. Войдите на портал Azure, если вы еще этого не сделали.
2. Откройте вкладку **Хранилище** .
3. Выберите учетную запись хранения, в которой находится VHD.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Откроются свойства учетной записи хранения. Откройте вкладку **Контейнеры** .
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Выберите контейнер, в котором хранится VHD. По умолчанию при создании на портале VHD сохраняется в контейнере виртуальных жестких дисков.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Выберите соответствующий VHD ОС, сравнив его URL-адрес с адресом, который вы сохранили.
7. Щелкните элемент **Загрузить**.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Загрузка VHD с помощью PowerShell
Наряду с порталом управления Azure для загрузки VHD ОС можно использовать командлет [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) .

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Например, Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> Командлет **Save-AzureVhd** также имеет параметр **NumberOfThreads**, позволяющий улучшить параллелизм и добиться оптимального использования доступной пропускной способности для загрузки.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Подключение VHD к учетной записи хранения Azure
Если VHD подготовлен локально, его необходимо отправить в учетную запись хранения в Azure. Это действие выполняется после локального создания VHD, но до сертификации образа виртуальной машины.

### <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера
Рекомендуется передавать VHD-диски в учетную запись хранения, размещенную в любом регионе на территории США. Все VHD для одного и того же SKU необходимо поместить в один и тот же контейнер в одной и той же учетной записи хранения.

Создать учетную запись хранения можно с помощью [портала Microsoft Azure](https://portal.azure.com/), PowerShell или программы командной строки Linux.  

**Создание учетной записи хранения на портале Microsoft Azure**

1. Щелкните **Создать ресурс**.
2. Выберите **Хранилище**.
3. Введите имя учетной записи хранения и выберите расположение.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Нажмите кнопку **Создать**.
5. Должна открыться колонка созданной учетной записи хранения. Если это не произошло, последовательно выберите **Обзор** > **Учетные записи хранения**. В колонке "Учетная запись хранения" выберите созданную учетную запись хранения.
6. Выберите **Контейнеры**.
   
   ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. В колонке "Контейнеры" выберите **Добавить**и введите имя и разрешения контейнера. Для разрешений контейнера выберите вариант **Частный** .

> [!TIP]
> Рекомендуется создавать по одному контейнеру для каждого номера SKU, который вы планируете опубликовать.
> 
> 

  ![рисунок](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Создание учетной записи хранения с помощью PowerShell
В PowerShell создайте учетную запись хранения с помощью командлета [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) .

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Затем создайте в этой учетной записи контейнер с помощью командлета [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) .

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Эти команды предполагают, что контекст текущей учетной записи хранения уже настроен в PowerShell.   Дополнительные сведения о настройке PowerShell см. в статье о [настройке Azure PowerShell](marketplace-publishing-powershell-setup.md).  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Создание учетной записи хранения с помощью программы командной строки для Mac и Linux
> В [программе командной строки Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)создайте учетную запись хранения следующим образом.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Создайте контейнер следующим образом.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Отправка VHD
Создав учетную запись хранения и контейнер, отправьте подготовленные VHD. Для этого можно использовать PowerShell, программу командной строки Linux или другие средства управления хранилищем Azure.

### <a name="upload-a-vhd-via-powershell"></a>Отправка VHD с помощью PowerShell
Воспользуйтесь командлетом [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) .

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Отправка VHD с помощью программы командной строки для Mac и Linux
В [программе командной строки Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) введите следующее: azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## <a name="see-also"></a>См. также
* [Создание образа виртуальной машины для Marketplace](marketplace-publishing-vm-image-creation.md)
* [Настройка Azure PowerShell](marketplace-publishing-powershell-setup.md)

