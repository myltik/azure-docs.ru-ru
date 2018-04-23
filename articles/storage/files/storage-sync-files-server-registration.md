---
title: Управление зарегистрированными серверами в службе" Синхронизация файлов Azure" (предварительная версия) | Документация Майкрософт
description: Узнайте, как зарегистрировать Windows Server или отменить его регистрацию в службе синхронизации хранилища службы синхронизации файлов Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 9367b2bdb1bb77725356d2be41d5e44d900cb927
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Управление зарегистрированными серверами в службе "Синхронизация файлов Azure" (предварительная версия)
Служба "Синхронизация файлов Azure" (предварительная версия) позволяет централизовано хранить файловые ресурсы организации в службе файлов Azure, обеспечивая гибкость, производительность и совместимость локального файлового сервера. Это достигается путем преобразования серверов Windows Server в быстрый кэш общего файлового ресурса Azure. Для локального доступа к данным вы можете использовать любой протокол (в том числе SMB, NFS и FTPS), доступный в Windows Server. Кроме того, вы можете создать любое число кэшей в любом регионе.

Следующая статья содержит сведения о регистрации сервера и управлении им в службе синхронизации хранилища. Сведения о полноценном процессе развертывания службы синхронизации файлов Azure см. в статье [Как развернуть службу синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md).

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Регистрация и отмена регистрации сервера в службе синхронизации хранилища
Регистрация сервера в службе синхронизации файлов Azure устанавливает отношения доверия между Windows Server и Azure. На основе этой связи на сервере можно создать *конечные точки сервера*, которые представляют определенные папки, синхронизируемые с общим файловым ресурсом Azure (которые также называется *облачная конечная точка*). 

### <a name="prerequisites"></a>предварительным требованиям
Чтобы зарегистрировать сервер в службе синхронизации хранилища, для него должны быть выполнены все предварительные требования.

* Сервер должен работать под управлением поддерживаемой версии Windows Server. Дополнительные сведения см. в разделе [Поддерживаемые версии Windows Server](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Убедитесь, что служба синхронизации хранилища развернута. Сведения о развертывании службы синхронизации хранилища см. в статье [Как развернуть службу синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md).
* Убедитесь, что сервер подключен к Интернету и вы имеете доступ к Azure.
* Отключите конфигурацию усиленной безопасности Internet Explorer для администраторов с помощью пользовательского интерфейса диспетчера сервера.
    
    ![Пользовательский интерфейс диспетчера сервера с выделенной конфигурацией усиленной безопасности Internet Explorer](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Убедитесь, что на сервере установлен модуль AzureRM PowerShell. Если сервер входит в отказоустойчивый кластер, для каждого узла кластера потребуется модуль AzureRM. Дополнительные сведения об установке модуля AzureRM см. в статье [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Установка и настройка Azure PowerShell).

    > [!Note]  
    > Мы рекомендуем использовать последнюю версию модуля AzureRM PowerShell для регистрации или отмены регистрации сервера. Если пакет AzureRM был ранее установлен на этом сервере (и на этом сервере используется версия PowerShell 5.* или выше), выполните командлет `Update-Module`, чтобы обновить этот пакет. 
* Если вы используете сетевой прокси-сервер в своей среде, настройте его параметры на сервере для использования агентом синхронизации.
    1. Определение IP-адреса и номера порта прокси-сервера
    2. Измените следующие два файла:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Добавьте строки, показанные на рисунке 1 (в этом разделе), в раздел /System.ServiceModel в двух файлах, приведенных выше, изменив 127.0.0.1:8888 на правильный IP-адрес (замените 127.0.0.1) и указав правильный номер порта (замените 8888).
    4. Задайте параметры прокси-сервера WinHTTP с помощью командной строки.
        * Отображение прокси-сервера: netsh winhttp show proxy
        * Настройка прокси-сервера: netsh winhttp set proxy 127.0.0.1:8888
        * Сброс прокси-сервера: netsh winhttp reset proxy
        * Если настройка осуществляется после установки агента, перезапустите агент синхронизации: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Регистрация сервера в службе синхронизации хранилища
Прежде чем использовать сервер в качестве *конечной точки сервера* в *группе синхронизации* службы "Синхронизация файлов Azure", его нужно зарегистрировать в *службе синхронизации хранилища*. Сервер не может быть зарегистрирован одновременно в нескольких службах синхронизации хранилища.

#### <a name="install-the-azure-file-sync-agent"></a>Установка агента службы синхронизации файлов Azure
1. [Скачайте агент службы синхронизации файлов Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Запустите установщик агента службы синхронизации файлов Azure.
    
    ![Первая область установщика агента службы синхронизации файлов Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Не забудьте включить обновление агента синхронизации файлов Azure с помощью Центра обновления Майкрософт. Это важно, так как важные исправления безопасности и усовершенствованные функции в пакете сервера поставляются через этот центр.

    ![Проверка включения Центра обновления Майкрософт на соответствующей панели установщика агента службы синхронизации файлов Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Если сервер не был зарегистрирован ранее, пользовательский интерфейс регистрации сервера откроется сразу после завершения установки.

> [!Important]  
> Если сервер входит в отказоустойчивый кластер, на каждом узле кластера нужно установить агент службы синхронизации файлов Azure.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Регистрация сервера с помощью пользовательского интерфейса регистрации сервера
> [!Important]  
> Подписки поставщиков облачных решений (CSP) не могут использовать пользовательский интерфейс регистрации сервера. Вместо этого используйте PowerShell (см. ниже в этом разделе).

1. Если пользовательский интерфейс регистрации сервера автоматически не запустился после завершения установки агента службы синхронизации файлов Azure, его можно запустить вручную, выполнив следующее `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Нажмите кнопку *Вход*, чтобы получить доступ к подписке Azure. 

    ![Стартовый экран процесса регистрации сервера](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. В диалоговом окне выберите правильную подписку, группу ресурсов и службу синхронизации хранилища.

    ![Сведения о службе синхронизации хранилища](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. В предварительной версии нужно выполнить еще один вход, чтобы завершить процесс. 

    ![Диалоговое окно входа](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Если сервер входит в отказоустойчивый кластер, каждый сервер нужно зарегистрировать. При просмотре зарегистрированных серверов на портале Azure служба "Синхронизация файлов Azure" автоматически распознает каждый узел в качестве члена отказоустойчивого кластера и группирует их соответствующим образом.

#### <a name="register-the-server-with-powershell"></a>Регистрация сервера с помощью PowerShell
Кроме того, можно выполнить регистрацию сервера с помощью PowerShell. Это единственный способ регистрации сервера для подписок поставщиков облачных решений (CSP):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Отмена регистрации сервера в службе синхронизации хранилища
Нужно выполнить несколько действий, чтобы отменить регистрацию сервера в службе синхронизации хранилища. Давайте рассмотрим, как отменить регистрацию сервера должным образом.

#### <a name="optional-recall-all-tiered-data"></a>Отзыв всех многоуровневых данных (необязательно)
Если для конечной точки сервера включено распределение по уровням облака, файлы будут *распределяться* по файловым ресурсам файлов Azure. Это позволит использовать локальные файловые ресурсы в качестве кэша, а не в качестве полной копии набора данных, чтобы обеспечить эффективное использование пространства файлового сервера. Но если конечная точка сервера будет удалена, пока распределенные файлы сохранены только локально на сервере, эти файлы станут недоступными. Таким образом, если нужен непрерывный доступ к файлу, вам понадобится отозвать все многоуровневые файлы из службы "Файлы Azure", прежде чем продолжить процесс отмены регистрации. 

Это можно сделать с помощью командлета PowerShell, как показано ниже.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Если на локальном томе, на котором размещена конечная точка сервера, недостаточно свободного места для восстановления всех распределенных данных, командлет `Invoke-StorageSyncFileRecall` завершится сбоем.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Удаление сервера из всех групп синхронизации
Прежде чем отменить регистрацию сервера в службе синхронизации хранилища, с этого сервера нужно удалить все конечные точки сервера. Это можно сделать на портале Azure.

1. Перейдите к службе синхронизации хранилища, в которой зарегистрирован сервер.
2. Удалите все конечные точки сервера в каждой группе синхронизации службы синхронизации хранилища. Для этого щелкните правой кнопкой мыши соответствующую конечную точку на панели группы синхронизации.

    ![Удаление конечной точки сервера из группы синхронизации](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Это также можно сделать с помощью простого сценария PowerShell.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Отмена регистрации сервера
Теперь, когда все данные восстановлены и сервер удален из всех групп синхронизации, можно отменить регистрацию этого сервера. 

1. На портале Azure перейдите к разделу *Registered servers* (Зарегистрированные серверы) для службы синхронизации хранилища.
2. Щелкните правой кнопкой мыши сервер, для которого нужно отменить регистрацию, и выберите "Отменить регистрацию сервера".

    ![Отмена регистрации сервера](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Соблюдение правил поведения службы "Синхронизация файлов Azure" в центре обработки данных 
Служба "Синхронизация файлов Azure" вряд ли будет единственной службой в вашем центре обработки данных. Скорее всего, вы не хотите, чтобы она использовала все ресурсы сети и хранилища, и установите ограничения.

> [!Important]  
> Если лимит будет слишком низким, пострадает производительность синхронизации и восстановления данных службой "Синхронизация файлов Azure".

### <a name="set-azure-file-sync-network-limits"></a>Настройка сетевых ограничений для службы "Синхронизация файлов Azure"
Можно регулировать использование сети службой "Синхронизация файлов Azure" с помощью командлетов `StorageSyncNetworkLimit`. 

Например, вы можете создать новое ограничение регулирования, не позволяющее службе "Синхронизация файлов Azure" использовать скорость выше 10 Мбит/с в период с 9:00 по 17:00 в рабочие дни. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Чтобы установить ограничения, примените следующий командлет:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Чтобы удалить ограничения сети, используйте `Remove-StorageSyncNetworkLimit`. Например, следующая команда удаляет все ограничения сети:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Использование функции качества обслуживания Windows Server для хранилища 
Если служба "Синхронизация файлов Azure" размещается на виртуальной машине под управлением узла виртуализации Windows Server, вы можете с помощью функции качества обслуживания ограничить потребление ресурсов хранилища. Политика качества обслуживания хранилища позволяет задать максимальный уровень (ограничение, которое действует аналогично описанным выше лимитам StorageSyncNetwork) или минимальный уровень (резервирование). Если использовать минимальные ограничения вместо максимальных, служба "Синхронизация файлов Azure" сможет активно использовать доступную пропускную способность хранилища, когда она не используется другими рабочими нагрузками. Дополнительные сведения см. в статье [Качество обслуживания хранилища](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>См. также
- [Планирование развертывания службы синхронизации файлов Azure (предварительная версия)](storage-sync-files-planning.md)
- [Развертывание службы синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md) 
- [Устранение неполадок службы синхронизации файлов Azure (предварительная версия)](storage-sync-files-troubleshoot.md)
