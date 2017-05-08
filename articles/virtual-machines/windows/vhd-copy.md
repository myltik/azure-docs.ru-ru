---
title: "Создание копии специализированной виртуальной машины в Azure | Документация Майкрософт"
description: "Узнайте, как создать копию специализированной виртуальной машины Windows, работающей в Azure, используя модель развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 1b2b013fb29c66817aab2ea5e63917b59726b4e8
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Создание копии специализированной виртуальной машины Windows, работающей в Azure
В этой статье показано, как с помощью инструмента AZCopy создать копию VHD со специализированной виртуальной машины Windows, работающей в Azure. Эту копию VHD можно затем использовать для создания другой виртуальной машины. 

* Если требуется скопировать универсальную виртуальную машину, то см. статью [Создание образа виртуальной машины на основе существующей виртуальной машины Azure](capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Если требуется отправить VHD с локальной виртуальной машины, например диск, созданный с помощью Hyper-V, то ознакомьтесь со статьей [Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Перед началом работы
Убедитесь, что выполнены следующие условия.

* У вас есть сведения об **исходной и целевой учетных записях хранения**. Для исходной виртуальной машины необходимы имена учетной записи хранения и контейнера. Как правило, имя контейнера — **vhds**. Необходимо также иметь целевую учетную запись хранения. Если вы ее еще не создали, то это можно сделать через портал (**Больше служб** > Учетные записи хранения > Добавить) или с помощью командлета [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). 
* У вас установлена служба Azure [PowerShell 1.0](/powershell/azure/overview) (или более поздней версии).
* Вы скачали и установили [инструмент AzCopy](../../storage/storage-use-azcopy.md). 

## <a name="deallocate-the-vm"></a>Освобождение виртуальной машины
Освободите виртуальную машину, что позволит скопировать VHD. 

* **Портал**: щелкните **Виртуальные машины** > **myVM** > Остановить
* **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` освобождает виртуальную машину с именем **myVM** в группе ресурсов **myResourceGroup**.

Вы увидите, что **состояние** виртуальной машины на портале Azure изменится с **Остановлено** на **Остановлено (освобождено)**.

## <a name="get-the-storage-account-urls"></a>Получение URL-адресов учетной записи хранения
Необходимо получить URL-адреса исходной и целевой учетных записей хранения. URL-адреса выглядят следующим образом: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Если вы уже знаете имена учетной записи хранения и контейнера, то можете просто подставить эти данные в скобки, чтобы создать URL-адрес. 

Для получения URL-адреса можно использовать портал Azure или Azure PowerShell:

* **Портал**: щелкните **Больше служб** > **Учетные записи хранения** > <storage account> **BLOB-объекты**. Ваш исходный VHD-файл скорее всего будет находиться в контейнере **vhds**. Щелкните **Свойства** контейнера и скопируйте текст с пометкой **URL-адрес**. Вам понадобятся URL-адреса исходного и целевого контейнеров. 
* **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` предоставляет сведения о виртуальной машине с именем **myVM** в группе ресурсов **myResourceGroup**. В результатах просмотрите раздел **Storage profile** (Профиль хранилища) и найдите в нем **URI VHD**. Первая часть URI является URL-адресом контейнера, а последняя часть — именем VHD операционной системы для виртуальной машины.

## <a name="get-the-storage-access-keys"></a>Получение ключей доступа к хранилищу
Найдите ключи доступа для исходной и целевой учетных записей хранения. Дополнительные сведения о ключах доступа см. в статье [Об учетных записях хранения Azure](../../storage/storage-create-storage-account.md).

* **Портал**: щелкните **Больше служб** > **Учетные записи хранения** > <storage account> **Все параметры** > **Ключи доступа**. Скопируйте ключ с пометкой **key1**.
* **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` предоставляет сведения о ключе к хранилищу данных для учетной записи хранения **mystorageaccount** в группе ресурсов **myResourceGroup**. Скопируйте ключ с пометкой **key1**.

## <a name="copy-the-vhd"></a>Копирование виртуального жесткого диска
С помощью AzCopy можно копировать файлы между учетными записями хранения. Если у вас нет указанного целевого контейнера, то он будет создан для вас. 

Чтобы воспользоваться AzCopy, откройте окно командной строки на локальном компьютере и перейдите к папке, в которой установлен инструмент AzCopy. Это будет папка вида *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Чтобы скопировать все файлы внутри контейнера, используйте параметр **/S**. Это действие можно использовать для копирования VHD операционной системы и всех дисков данных, если они находятся в одном контейнере. В этом примере показано, как скопировать все файлы из контейнера **mysourcecontainer** в учетной записи хранения **mysourcestorageaccount** в контейнер **mydestinationcontainer** в учетной записи хранения **mydestinationstorageaccount**. Замените имена учетных записей хранения и контейнеров своими собственными. Замените ключи `<sourceStorageAccountKey1>` и `<destinationStorageAccountKey1>` своими собственными.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Если требуется скопировать только один VHD из контейнера с несколькими файлами, то можно указать имя файла с помощью параметра /Pattern. В данном примере будет скопирован только файл с именем **myFileName.vhd**.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


По завершении отобразится сообщение следующего вида:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Устранение неполадок
* Если при использовании AZCopy отображается сообщение об ошибке "Серверу не удалось проверить подлинность этого запроса", убедитесь, что значение заголовка авторизации составлено правильно (содержит подпись). Если используется ключ key2 или вторичный ключ к хранилищу данных, попробуйте воспользоваться первичным ключом или первым ключом к хранилищу данных.

## <a name="next-steps"></a>Дальнейшие действия
* Можно создать виртуальную машину, [подключив копию VHD к виртуальной машине в качестве диска ОС](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


