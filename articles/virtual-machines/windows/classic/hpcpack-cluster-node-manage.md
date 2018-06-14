---
title: Управление вычислительными узлами кластера пакета HPC | Документация Майкрософт
description: Узнайте о сценариях PowerShell для добавления, удаления, запуска и остановки вычислительных узлов кластера пакета HPC 2012 R2 в Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 453f53be15b24b96f183b4935cc45fc97ad058bd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914941"
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Управление числом и доступностью вычислительных узлов в кластере пакета HPC в Azure
Если вы создали кластер пакета HPC 2012 R2 на виртуальных машинах Azure, вам может потребоваться простой способ добавления, удаления, запуска (подготовки) или остановки (отзыва) нескольких виртуальных машин вычислительных узлов в кластере. Для выполнения этих задач запустите сценарии Azure PowerShell, которые установлены на виртуальной машине головного узла. Эти сценарии позволяют контролировать количество и доступность ресурсов кластера пакета HPC, а также затраты на них.

> [!IMPORTANT] 
> Эта статья относится только к кластерам пакета HPC 2012 R2 в Azure, созданным с помощью классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.
> Кроме того, сценарии PowerShell, описанные в этой статье, недоступны в пакете HPC 2016.

## <a name="prerequisites"></a>предварительным требованиям
* **Кластер пакета HPC 2012 R2 на виртуальных машинах Azure.** Создайте кластер пакета HPC 2012 R2 в классической модели развертывания. Например, можно автоматизировать развертывание с помощью образа виртуальной машины пакета HPC 2012 R2 в Azure Marketplace и сценария Azure PowerShell. Дополнительные сведения и описание необходимых компонентов см. в статье [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](hpcpack-cluster-powershell-script.md).
  
    После развертывания найдите сценарии управления узлом в папке %CCP\_HOME%bin на головном узле. Каждый из сценариев следует запускать от имени администратора:
* **Файл параметров публикации или сертификат управления Azure.** На головном узле необходимо выполнить одно из следующих действий:
  
  * **Импортируйте файл параметров публикации Azure**. Чтобы сделать это, запустите на головном узле следующие командлеты Azure PowerShell:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Настройте сертификат управления Azure на головном узле**. При наличии CER-файла импортируйте его в CurrentUser\My certificate store, а затем запустите следующий командлет Azure PowerShell для своей среды Azure (AzureCloud или AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Добавление виртуальных машин вычислительных узлов
Добавьте вычислительные узлы с помощью сценария **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Параметры
* **ServiceName** — это имя облачной службы, в которую будут добавляться новые виртуальные машины вычислительных узлов.
* **ImageName** — это имя образа виртуальной машины Azure, который можно получить с помощью портала Azure или командлета Azure PowerShell **Get-AzureVMImage**. Этот образ должен соответствовать следующим требованиям.
  
  1. Должна быть установлена операционная система Windows.
  2. В роли вычислительного узла должен быть установлен пакет HPC.
  3. Образ должен относится к частным образам из категории пользовательских, а не к общедоступным образам виртуальной машины Azure.
* **Quantity** — это число добавляемых виртуальных машин вычислительных узлов.
* **InstanceSize** — это размер виртуальных машин вычислительных узлов.
* **DomainUserName** — это имя пользователя домена, которое будет использоваться для присоединения новых виртуальных машин к домену.
* **DomainUserPassword** — это пароль пользователя домена.
* **NodeNameSeries** (необязательно) — это шаблон именования для вычислительных узлов. Требуемый формат: &lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;%. Например, MyCN%10% означает последовательность имен вычислительных узлов, начинающуюся с MyCN11. Если не указано, сценарий использует настроенную последовательность имен узлов в кластере HPC.

### <a name="example"></a>Пример
В следующем примере 20 виртуальных машин вычислительных узлов большого размера добавляются в облачную службу *hpcservice1* на основе образа виртуальной машины *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Удаление виртуальных машин вычислительных узлов
Удалите вычислительные узлы с помощью сценария **Remove-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Параметры
* **Name** — это имена узлов кластера для удаления. Поддерживаются подстановочные знаки. Для параметра задано имя Name. Нельзя одновременно задать параметры **Name** и **Node**.
* **Node** — это объект HpcNode для удаляемых узлов, который можно получить с помощью командлета PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Для параметра задано имя Node. Нельзя одновременно задать параметры **Name** и **Node**.
* **DeleteVHD** (необязательно) — это параметр удаления связанных дисков для удаляемых виртуальных машин.
* **Force** (необязательно) — это параметр принудительного отключения узлов HPC перед их удалением.
* **Confirm** (необязательно) — это запрос подтверждения перед выполнением команды.
* **WhatIf** — это параметр описания последствий выполнения команды без фактического выполнения этой команды.

### <a name="example"></a>Пример
В следующем примере принудительно отключаются узлы, имена которых начинаются с *HPCNode-CN-*, после чего эти узлы и связанные с ними диски удаляются.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Запуск виртуальных машин вычислительных узлов
Запустите вычислительные узлы с помощью сценария **Start-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Параметры
* **Name** — это имена узлов кластера для запуска. Поддерживаются подстановочные знаки. Для параметра задано имя Name. Нельзя одновременно задать параметры **Name** и **Node**.
* **Node**— объект HpcNode для запускаемых узлов, который можно получить с помощью командлета PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Для параметра задано имя Node. Нельзя одновременно задать параметры **Name** и **Node**.

### <a name="example"></a>Пример
В следующем примере запускаются узлы, имена которых начинаются с *HPCNode-CN-*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Остановка виртуальных машин вычислительных узлов
Остановите вычислительные узлы с помощью сценария **Stop-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Синтаксис
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Параметры
* **Name**— имена узлов кластера для остановки. Поддерживаются подстановочные знаки. Для параметра задано имя Name. Нельзя одновременно задать параметры **Name** и **Node**.
* **Node** — это объект HpcNode для останавливаемых узлов, который можно получить с помощью командлета PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Для параметра задано имя Node. Нельзя одновременно задать параметры **Name** и **Node**.
* **Force** (необязательно) — это параметр принудительного отключения узлов HPC перед их остановкой.

### <a name="example"></a>Пример
В следующем примере принудительно отключаются узлы, имена которых начинаются с *HPCNode-CN-* , после чего эти узлы останавливаются.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Дополнительная информация
* Если вам требуется, чтобы число узлов кластера автоматически увеличивалось или уменьшалось в соответствии с текущей рабочей нагрузкой заданий и задач в кластере, см. статью [Автоматическое изменение размера ресурсов кластера пакета HPC в Azure в соответствии с рабочей нагрузкой кластера](hpcpack-cluster-node-autogrowshrink.md).

