---
title: "Регистрация и отмена регистрации сервера в службе синхронизации файлов Azure (предварительная версия) | Документация Майкрософт"
description: "Узнайте, как зарегистрировать Windows Server или отменить его регистрацию в службе синхронизации хранилища службы синхронизации файлов Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 13a75d5cafd94435346660614721399f2d77919b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Регистрация и отмена регистрации сервера в службе синхронизации файлов Azure (предварительная версия)
Служба "Синхронизация файлов Azure" (предварительная версия) позволяет централизовано хранить файловые ресурсы организации в службе файлов Azure, обеспечивая гибкость, производительность и совместимость локального файлового сервера. Это достигается путем преобразования серверов Windows Server в быстрый кэш общего файлового ресурса Azure. Для локального доступа к данным вы можете использовать любой протокол (в том числе SMB, NFS и FTPS), доступный в Windows Server. Кроме того, вы можете создать любое число кэшей в любом регионе.

Следующая статья содержит сведения о регистрации и отмене регистрации сервера в службе синхронизации хранилища. Это может быть полезным, если сервер выведен из эксплуатации или даже если конечную точку сервера нужно использовать в группе синхронизации. Сведения о полноценном процессе развертывания службы синхронизации файлов Azure см. в статье [Как развернуть службу синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы зарегистрировать Windows Server в службе синхронизации хранилища, для Windows Server должны быть выполнены все предварительные требования.

* Убедитесь, что служба синхронизации хранилища развернута. Сведения о развертывании службы синхронизации хранилища см. в статье [Как развернуть службу синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md).
* Убедитесь, что сервер подключен к Интернету и вы имеете доступ к Azure.
* Отключите конфигурацию усиленной безопасности Internet Explorer для администраторов с помощью пользовательского интерфейса диспетчера сервера.
    
    ![Пользовательский интерфейс диспетчера сервера с выделенной конфигурацией усиленной безопасности Internet Explorer](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Убедитесь, что на сервере установлен модуль AzureRM PowerShell. Если сервер входит в отказоустойчивый кластер, для каждого узла кластера потребуется модуль AzureRM. Дополнительные сведения об установке модуля AzureRM см. в статье [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Установка и настройка Azure PowerShell).

    > [!Note]  
    > Мы рекомендуем использовать последнюю версию модуля AzureRM PowerShell для регистрации или отмены регистрации сервера. Если пакет AzureRM был ранее установлен на этом сервере (и на этом сервере используется версия PowerShell 5.* или выше), выполните командлет `Update-Module`, чтобы обновить этот пакет. 

## <a name="register-a-server-with-storage-sync-service"></a>Регистрация сервера в службе синхронизации хранилища
Прежде чем Windows Server можно будет использовать в качестве *конечной точки сервера* в *группе синхронизации* службы синхронизации файлов Azure, он должен быть зарегистрирован в *службе синхронизации хранилища*. Сервер можно зарегистрировать только в одной *службе синхронизации хранилища* за раз.

### <a name="install-the-azure-file-sync-agent"></a>Установка агента службы синхронизации файлов Azure
1. [Скачайте агент службы синхронизации файлов Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Запустите установщик агента службы синхронизации файлов Azure.
    
    ![Первая область установщика агента службы синхронизации файлов Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Не забудьте включить обновление агента синхронизации файлов Azure с помощью Центра обновления Майкрософт. Это важно, так как важные исправления безопасности и усовершенствованные функции в пакете сервера поставляются через этот центр.

    ![Проверка включения Центра обновления Майкрософт на соответствующей панели установщика агента службы синхронизации файлов Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Если сервер не был зарегистрирован ранее, пользовательский интерфейс регистрации сервера откроется сразу после завершения установки.

> [!Important]  
> Если сервер входит в отказоустойчивый кластер, на каждом узле кластера нужно установить агент службы синхронизации файлов Azure.

### <a name="register-the-server-using-the-server-registration-ui"></a>Регистрация сервера с помощью пользовательского интерфейса регистрации сервера

> [!Important]  
> Подписки поставщика облачных решений не могут использовать пользовательский интерфейс регистрации сервера. Вместо этого используйте PowerShell (см. ниже в этом разделе).

1. Если пользовательский интерфейс регистрации сервера автоматически не запустился после завершения установки агента службы синхронизации файлов Azure, его можно запустить вручную, выполнив следующее `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Нажмите кнопку *Вход*, чтобы получить доступ к подписке Azure. 

    ![Открытие диалогового окна пользовательского интерфейса регистрации сервера](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. В диалоговом окне выберите правильную подписку, группу ресурсов и службу синхронизации хранилища.

    ![Сведения о службе синхронизации хранилища](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. В предварительной версии нужно выполнить еще один вход, чтобы завершить процесс. 

    ![Диалоговое окно входа](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Если сервер входит в отказоустойчивый кластер, каждый сервер нужно зарегистрировать. При просмотре зарегистрированных серверов на портале Azure служба синхронизации файлов Azure автоматически распознает каждый узел в качестве члена одного отказоустойчивого кластера и группирует их вместе соответствующим образом.

### <a name="register-the-server-with-powershell"></a>Регистрация сервера с помощью PowerShell
Кроме того, можно выполнить регистрацию сервера с помощью PowerShell. Это единственный способ регистрации сервера для подписок поставщика облачных решений:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

## <a name="unregister-the-server-with-storage-sync-service"></a>Отмена регистрации сервера в службе синхронизации хранилища
Нужно выполнить несколько действий, чтобы отменить регистрацию сервера в службе синхронизации хранилища. Давайте рассмотрим, как отменить регистрацию сервера должным образом.

### <a name="optional-recall-all-tiered-data"></a>Отзыв всех многоуровневых данных (необязательно)
При включении для конечной точки сервера функция распределения по уровням облака *распределит* файлы по уровням в файловых ресурсах Azure. Это позволит использовать локальные файловые ресурсы в качестве кэша, а не в качестве полной копии набора данных, чтобы обеспечить эффективное использование пространства файлового сервера. Тем не менее, если удаленная конечная точка сервера вместе с многоуровневыми файлами по-прежнему остается на сервере, эти файлы станут недоступными. Таким образом, если нужен непрерывный доступ к файлу, вам понадобится отозвать все многоуровневые файлы из службы "Файлы Azure", прежде чем продолжить процесс отмены регистрации. 

Это можно сделать с помощью командлета PowerShell, как показано ниже.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Если на локальном томе, на котором размещен сервер, недостаточно свободного места для отзыва всех многоуровневых данных, командлет `Invoke-StorageSyncFileRecall` завершится сбоем.  

### <a name="remove-the-server-from-all-sync-groups"></a>Удаление сервера из всех групп синхронизации
Прежде чем отменить регистрацию сервера в службе синхронизации хранилища, с этого сервера нужно удалить все конечные точки сервера. Это можно сделать через портал.

1. Перейдите к службе синхронизации хранилища, в которой зарегистрирован сервер.
2. Удалите все конечные точки сервера в каждой группе синхронизации службы синхронизации хранилища. Это можно сделать, щелкнув правой кнопкой мыши соответствующую конечную точку на панели группы синхронизации.

    ![Удаление конечной точки сервера из группы синхронизации](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Это также можно сделать с помощью простого сценария PowerShell.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Отмена регистрации сервера
Теперь, когда все данные были отозваны и сервер был удален из всех групп синхронизации, можно отменить регистрацию сервера. 

1. На портале Azure перейдите к разделу зарегистрированных серверов службы синхронизации хранилища.
2. Щелкните правой кнопкой мыши сервер, для которого нужно отменить регистрацию, и выберите "Отменить регистрацию сервера".

    ![Отмена регистрации сервера](media/storage-sync-files-server-registration/unregister-server-1.png)