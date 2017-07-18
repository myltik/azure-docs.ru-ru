---
title: "Как создать общую папку Azure | Документация Майкрософт"
description: "Как создать общую папку Azure в хранилище файлов Azure с помощью портала Azure, PowerShell и Azure CLI."
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
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: bfe12fbdd50e82404ff49b1e34adc03913e50905
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>Создание общей папки в хранилище файлов Azure
Вы можете создавать общие папки Azure с помощью [портала Azure](https://portal.azure.com/), командлетов PowerShell службы хранилища Azure, клиентских библиотек службы хранилища Azure и интерфейса REST API службы хранилища Azure. В рамках этого руководства вы узнаете следующее:
* [Как создать общую папку Azure с помощью портала Azure](#Create file share through the Portal)
* [Как создать общую папку Azure с помощью PowerShell](#Create file share using PowerShell)
* [Как создать общую папку Azure с помощью CLI](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Предварительные требования
Чтобы создать общую папку Azure, вы можете использовать существующую учетную запись хранения или [создать новую учетную запись хранения Azure](storage-create-storage-account.md). Чтобы создать общую папку Azure с помощью PowerShell, вам потребуется ключ учетной записи и имя учетной записи хранения. Если вы планируете использовать PowerShell или интерфейс командной строки, вам потребуется ключ учетной записи хранения.

## <a name="create-file-share-through-the-portal"></a>Создание общей папки с помощью портала
1. **Перейдите в колонку учетной записи хранения на портале Azure**:    
    ![Колонка учетной записи хранения](media/storage-file-how-to-create-file-share/create-file-share-portal1.png)

2. **Нажмите кнопку "Общая папка"**:    
    ![Кнопка "Общая папка"](media/storage-file-how-to-create-file-share/create-file-share-portal2.png)

3. **Укажите имя и квоту. В настоящее время предусмотрено ограничение квоты — не более 5 ТБ**:    
    ![Указание имени и необходимой квоты для новой общей папки](media/storage-file-how-to-create-file-share/create-file-share-portal3.png)

4. **Просмотрите новую общую папку**: ![просмотр новой общей папки](media/storage-file-how-to-create-file-share/create-file-share-portal4.png)

5. **Отправьте файл**: ![отправка файла](media/storage-file-how-to-create-file-share/create-file-share-portal5.png)

6. **Перейдите в общую папку для управления каталогами и файлами**: ![обзор общей папки](media/storage-file-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Создание общей папки с помощью PowerShell
Для подготовки к использованию PowerShell загрузите и установите командлеты Azure PowerShell. Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).

> [!Note]  
> Рекомендуется загрузить и установить или обновить версию модуля Azure PowerShell.

1. **Создайте контекст для учетной записи хранения и ключ**. Контекст инкапсулирует имя и ключ учетной записи хранения. Инструкции по копированию ключа учетной записи с [портала Azure](https://portal.azure.com/) см. в разделе [Просмотр и копирование ключей доступа к хранилищу](storage-create-storage-account.md#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Создайте новую общую папку**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Имя общей папки должно состоять из символов в нижнем регистре. Дополнительные сведения о присвоении имен общим папкам и файлам см. в статье [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Именование общих ресурсов, каталогов, файлов и метаданных и ссылка на них).

## <a name="create-file-share-through-command-line-interface-cli"></a>Создание общей папки с помощью интерфейса командной строки (CLI)
1. **Чтобы подготовиться к использованию интерфейса командной строки (CLI), загрузите и установите Azure CLI.**  
    Дополнительные сведения см. в руководстве по [установке Azure CLI 2.0](/cli/azure/install-az-cli2.md), а также руководстве по [обзору Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md).

2. **Создайте строку подключения для учетной записи хранения, где нужно создать общую папку.**  
    В примере ниже замените значения ```<storage-account>``` и ```<resource_group>``` именем учетной записи хранения и именем группы ресурса.

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Создайте общую папку**.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Mount an Azure File share and access the share in Windows](storage-file-how-to-use-files-windows.md) (Подключение файлового ресурса Azure и доступ к нему в Windows)
* [Использование хранилища файлов Azure в Linux](storage-how-to-use-files-linux.md)
* [Mount Azure File share over SMB with macOS](storage-file-how-to-use-files-mac.md) (Использование хранилища файлов Azure с помощью протокола SMB в macOS)

Дополнительную информацию о хранилище файлов Azure см. по этим ссылкам.

* [Часто задаваемые вопросы](storage-files-faq.md)
* [Устранение неполадок](storage-troubleshoot-file-connection-problems.md)
