---
title: "Как управлять службой файлов Azure с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как использовать PowerShell для управления службой файлов Azure."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: f919e1880f709b416867a29de14f1dcc63a165fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-powershell-to-manage-azure-files"></a>Как использовать PowerShell для управления службой файлов Azure
Вы можете использовать Azure PowerShell для создания общих папок и управления ими.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Установите командлеты PowerShell для хранилища Azure
Для подготовки к использованию PowerShell загрузите и установите командлеты Azure PowerShell. Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Рекомендуется загрузить и установить или обновить версию модуля Azure PowerShell.
> 
> 

Откройте окно Azure PowerShell. Для этого нажмите кнопку **Запустить** и введите **Windows PowerShell**. Окно PowerShell выполнит загрузку модуля Azure PowerShell.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Создание объекта контекста и ключа для учетной записи хранения
Создайте объект контекста учетной записи хранения. Контекст включает имя учетной записи хранения и ключ. Инструкции по копированию ключа учетной записи с [портала Azure](https://portal.azure.com) см. в разделе [Просмотр и копирование ключей доступа к хранилищу](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

В следующем примере вместо `storage-account-name` и `storage-account-key` используйте имя и ключ своей учетной записи хранения.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Создание нового ресурса совместно используемых файлов
Создайте новый файловый ресурс с именем `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

У вас появится новый общий ресурс в хранилище файлов. Далее мы добавим каталог и файл.

> [!IMPORTANT]
> Имя общей папки должно состоять из символов в нижнем регистре. Дополнительные сведения о присвоении имен общим папкам и файлам см. в статье [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Именование общих ресурсов, каталогов, файлов и метаданных и ссылка на них).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Создание каталога в ресурсе совместно используемых файлов
Создайте каталог в ресурсе совместно используемых файлов. В данном примере используется каталог с именем `CustomLogs`:

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Отправка локального файла в каталог
Затем загрузите локальный файл в хранилище файлов. В данном примере файл передается из `C:\temp\Log1.txt`. Отредактируйте путь к файлу, чтобы он соответствовал пути к существующему файлу на локальном компьютере:

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>Просмотр списка файлов в каталоге
Чтобы просмотреть файлы в каталоге, можно вывести список файлов. Эта команда возвращает файлы и подкаталоги (если таковые имеются) в каталоге CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile возвращает список файлов и каталогов в каталоге, в который отправлен объект. Get-AzureStorageFile -Share $s возвращает список файлов и каталогов в корневой папке. Чтобы получить список файлов в подкаталоге, необходимо передать подкаталог команде Get-AzureStorageFile. Как это работает: первая часть команды возвращает экземпляр каталога подкаталога CustomLogs. Затем этот экземпляр передается команде Get-AzureStorageFile, которая возвращает файлы и каталоги в CustomLogs.

## <a name="copy-files"></a>Копирование файлов
Начиная с версии 0.9.7 Azure PowerShell, можно скопировать файл в другой файл, файл в большой двоичный объект или BLOB-объект в файл. Ниже показано, как выполнить копирование с помощью командлетов PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию о службе файлов Azure см. по следующим ссылкам.

* [Часто задаваемые вопросы](../storage-files-faq.md)
* [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Windows)      
* [Troubleshoot Azure File storage problems in Linux](storage-troubleshoot-linux-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Linux)    