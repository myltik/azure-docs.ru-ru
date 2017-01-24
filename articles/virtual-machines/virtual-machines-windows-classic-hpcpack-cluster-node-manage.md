---
title: "Управление вычислительными узлами кластера пакета HPC | Документация Майкрософт"
description: "Дополнительные сведения о сценариях PowerShell для добавления, удаления, запуска и остановки вычислительных узлов кластера пакета HPC в Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 3cd3c847eb476c783d09f5f18a5a54a75ade6c5f


---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Управление числом и доступностью вычислительных узлов в кластере пакета HPC в Azure
Если вы создали кластер пакета HPC на виртуальных машинах Azure, вам может потребоваться простой способ добавления, удаления, запуска (подготовки) или остановки (отзыва) нескольких виртуальных машин вычислительных узлов в кластере. Для выполнения этих задач запустите сценарии Azure PowerShell, которые установлены на виртуальной машине головного узла. Эти сценарии позволяют контролировать количество и доступность ресурсов кластера пакета HPC, а также затраты на них.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

## <a name="prerequisites"></a>Предварительные требования
* **Кластер пакета HPC на виртуальных машинах Azure.** Создайте кластер пакета HPC в классической модели развертывания с помощью пакета HPC 2012 R2 с обновлением 1 или более новой версии. Например, можно автоматизировать развертывание с помощью текущего образа виртуальной машины пакета HPC в Azure Marketplace и сценария Azure PowerShell. Дополнительные сведения и описание необходимых компонентов см. в статье [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  
    После развертывания найдите сценарии управления узлом в папке %CCP\_HOME%bin на головном узле. Каждый из сценариев следует запускать от имени администратора.
* **Файл параметров публикации или сертификат управления Azure** — необходимо выполнить на головном узле одно из следующих действий:
  
  * **Импортируйте файл параметров публикации Azure**. Чтобы сделать это, запустите на головном узле следующие командлеты Azure PowerShell:
    
    ```
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Настройте сертификат управления Azure на головном узле**. При наличии CER-файла импортируйте его в CurrentUser\My certificate store, а затем запустите следующий командлет Azure PowerShell для своей среды Azure (AzureCloud или AzureChinaCloud):
    
    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Добавление виртуальных машин вычислительных узлов
Добавьте вычислительные узлы с помощью сценария **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Параметры
* **ServiceName** — имя облачной службы, в которую будут добавляться новые виртуальные машины вычислительных узлов.
* **ImageName** — имя образа виртуальной машины Azure, который можно получить с помощью классического портала Azure или командлета Azure PowerShell **Get-AzureVMImage**. Этот образ должен соответствовать следующим требованиям.
  
  1. Должна быть установлена операционная система Windows.
  2. В роли вычислительного узла должен быть установлен пакет HPC.
  3. Образ должен относится к частным образам из категории пользовательских, а не к общедоступным образам виртуальной машины Azure.
* **Quantity** — число добавляемых виртуальных машин вычислительных узлов.
* **InstanceSize** — размер виртуальных машин вычислительных узлов.
* **DomainUserName** — имя пользователя домена, которое будет использоваться для присоединения новых виртуальных машин к домену.
* **DomainUserPassword** — пароль пользователя домена.
* **NodeNameSeries** (необязательно) — шаблон именования для вычислительных узлов. Требуемый формат: &lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;%. Например, MyCN%10% означает последовательность имен вычислительных узлов, начинающуюся с MyCN11. Если не указано, сценарий использует настроенную последовательность имен узлов в кластере HPC.

### <a name="example"></a>Пример
В следующем примере 20 виртуальных машин вычислительных узлов большого размера добавляются в облачную службу *hpcservice1* на основе образа виртуальной машины *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Удаление виртуальных машин вычислительных узлов
Удалите вычислительные узлы с помощью сценария **Remove-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Параметры
* **Name** — имена узлов кластера для удаления. Поддерживаются подстановочные знаки. Для параметра задано имя Name. Нельзя одновременно задать параметры **Name** и **Node**.
* **Node** — объект HpcNode для удаляемых узлов, который можно получить с помощью командлета PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Для параметра задано имя Node. Нельзя одновременно задать параметры **Name** и **Node**.
* **DeleteVHD** (необязательно) — параметр удаления связанных дисков для удаляемых виртуальных машин.
* **Force** (необязательно) — параметр принудительного отключения узлов HPC перед их удалением.
* **Confirm** (необязательно) — запрос подтверждения перед выполнением команды.
* **WhatIf** — параметр описания последствий выполнения команды без фактического выполнения этой команды.

### <a name="example"></a>Пример
В следующем примере принудительно отключаются узлы, имена которых начинаются с *HPCNode-CN-* , после чего эти узлы и связанные с ними диски удаляются.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Запуск виртуальных машин вычислительных узлов
Запустите вычислительные узлы с помощью сценария **Start-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Параметры
* **Name** — имена узлов кластера для запуска. Поддерживаются подстановочные знаки. Для параметра задано имя Name. Нельзя одновременно задать параметры **Name** и **Node**.
* **Node**— объект HpcNode для запускаемых узлов, который можно получить с помощью командлета PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Для параметра задано имя Node. Нельзя одновременно задать параметры **Name** и **Node**.

### <a name="example"></a>Пример
В следующем примере запускаются узлы, имена которых начинаются с *HPCNode-CN-*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Остановка виртуальных машин вычислительных узлов
Остановите вычислительные узлы с помощью сценария **Stop-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Параметры
* **Name**— имена узлов кластера для остановки. Поддерживаются подстановочные знаки. Для параметра задано имя Name. Нельзя одновременно задать параметры **Name** и **Node**.
* **Node** — объект HpcNode для останавливаемых узлов, который можно получить с помощью командлета PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Для параметра задано имя Node. Нельзя одновременно задать параметры **Name** и **Node**.
* **Force** (необязательно) — параметр принудительного отключения узлов HPC перед их остановкой.

### <a name="example"></a>Пример
В следующем примере принудительно отключаются узлы, имена которых начинаются с *HPCNode-CN-* , после чего эти узлы останавливаются.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## <a name="next-steps"></a>Дальнейшие действия
* Если вам требуется, чтобы число узлов кластера автоматически увеличивалось или уменьшалось в соответствии с текущей рабочей нагрузкой заданий и задач в кластере, см. статью [Автоматическое масштабирование вычислительных ресурсов Azure в кластере пакета HPC согласно рабочей нагрузке кластера](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Dec16_HO1-->


