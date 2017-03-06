---
title: "Управление записью пакетов с помощью Наблюдателя за сетями Azure (Azure CLI) | Документация Майкрософт"
description: "На этой странице объясняется, как управлять функцией записи пакетов Наблюдателя за сетями с помощью Azure CLI."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d2a65104743d9497debdc85c134fd1a06114c514
ms.openlocfilehash: 279664a51eab79c42a14ed9c9bb5f65cc43aaab2
ms.lasthandoff: 02/23/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli"></a>Управление записью пакетов с помощью Наблюдателя за сетями Azure в Azure CLI

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Возможность записи пакетов Наблюдателя за сетями позволяет создавать сеансы записи для отслеживания входящего и исходящего трафика виртуальной машины. Для сеанса записи предоставляются фильтры, которые позволяют убедиться, что записывается только требуемый трафик. Записи пакетов помогают выявить аномалии в работе сети по факту или заранее. Они также помогают выполнять сбор сетевой статистики, получать сведения о сетевых вторжениях, выполнять отладку передачи данных между клиентом и сервером и многое другое. Так как запись пакетов активируется удаленно, ее не нужно запускать вручную. К тому же она сразу выполняется на требуемой виртуальной машине, что также позволяет сэкономить ценное время.

В этой статье вы ознакомитесь с разными задачами управления, доступными в настоящее время для записи пакетов.

- [**Запуск записи пакета**](#start-a-packet-capture)
- [**Прекращение записи пакета**](#stop-a-packet-capture)
- [**Удаление записи пакета**](#delete-a-packet-capture)
- [**Скачивание записи пакета**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Перед началом работы

В данной статье предполагается, что у вас есть следующие ресурсы:

- экземпляр Наблюдателя за сетями в регионе, в котором нужно создать запись пакетов;
- виртуальная машина с включенным расширением для записи пакетов.

> [!IMPORTANT]
> Для записи пакетов требуется, чтобы на виртуальной машине был запущен агент. Агент устанавливается в качестве расширения. Инструкции по расширениям виртуальных машин см. в статье [Обзор расширений и компонентов виртуальной машины под управлением Windows](../virtual-machines/virtual-machines-windows-extensions-features.md).

## <a name="install-vm-extension"></a>Установка расширения виртуальной машины

### <a name="step-1"></a>Шаг 1

Выполните командлет `azure vm extension set`, чтобы установить агент записи пакетов на гостевой виртуальной машине.

Для виртуальных машин Windows:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r anyExtensionReferenceName -n NetworkWatcherAgentWindows -o 1.4
```

### <a name="step-2"></a>Шаг 2

Чтобы убедиться, что агент установлен, выполните командлет `vm extension get` и передайте ему имя группы ресурсов и виртуальной машины. Проверьте итоговый список, чтобы убедиться, что агент установлен.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

Ниже приведен пример ответа после выполнения операции `azure vm extension get`.

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                     Version  State
data:    ------------------------------  -----------------------  -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentTest  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>Запуск записи пакета

После выполнения предыдущих шагов на виртуальной машине будет установлен агент записи пакетов.

### <a name="step-1"></a>Шаг 1

Далее необходимо извлечь экземпляр Наблюдателя за сетями. Эта переменная передается в командлет `network watcher show` на шаге 4.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>Шаг 2

Получите учетную запись хранения. Она используется для хранения файла записи пакетов.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Шаг 3.

С помощью фильтров можно ограничить данные, которые сохраняются при записи пакетов. В следующем примере настраивается запись пакетов с несколькими фильтрами.  Первые три фильтра собирают исходящий TCP-трафик только с локального IP-адреса 10.0.0.3 на порты назначения 20, 80 и 443.  Последний фильтр собирает только трафик, передаваемый по протоколу UDP.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Для записи пакетов можно определить несколько фильтров. Если используется сложная структура фильтров, во избежание синтаксических ошибок лучше применять фильтры в виде JSON-файлов. Например, используйте флаг -r (вместо -f) и передайте расположение JSON-файла, содержащего следующие фильтры:

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


Ниже приведен пример ожидаемого результата выполнения командлета `network watcher packet-capture create`.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>Получение записи пакета

При выполнении командлета `network watcher packet-capture show` вы получаете сведения о состоянии выполняющейся или завершенной записи пакетов.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

Ниже представлен пример выходных данных командлета `network watcher packet-capture show`, полученных после завершения записи пакетов. В качестве значения параметра PacketCaptureStatus указано Stopped, а для параметра StopReason задано значение TimeExceeded. По этому значению можно понять, что запись пакетов выполнена успешно за требуемое время.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>Прекращение записи пакета

Если выполнить командлет `network watcher packet-capture stop` во время сеанса записи пакета, он будет остановлен.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> При выполнении во время текущего сеанса записи или в имеющемся остановленном сеансе командлет не возвратит ответ.

## <a name="delete-a-packet-capture"></a>Удаление записи пакета

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> При удалении записи пакета файл в учетной записи хранения не удаляется.

## <a name="download-a-packet-capture"></a>Скачивание записи пакета

После завершения сеанса записи пакета файл записи можно передать в хранилище BLOB-объектов или в локальный файл на виртуальной машине. Место хранения записи пакетов определяется при создании сеанса. Удобное средство для доступа к этим файлам записи, сохраненным в учетной записи хранения — обозреватель службы хранилища Microsoft Azure, который можно скачать по адресу http://storageexplorer.com/.

При указании учетной записи хранения файлы записи пакетов сохраняются в ней по следующему адресу:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как автоматизировать запись пакетов, используя оповещения на виртуальной машине, в статье [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md) (Создание записи пакетов, активируемой с использованием оповещений).

Сведения о состоянии (разрешен или запрещен) входящего и исходящего трафика виртуальной машины см. в статье [Check IP flow verify](network-watcher-check-ip-flow-verify-portal.md) (Проверка потока IP-адресов).

<!-- Image references -->

