---
title: Создание пиринга виртуальных сетей Azure, развернутых с помощью Resource Manager в одной подписке | Документация Майкрософт
description: Узнайте, как создать пиринг между виртуальными сетями, развернутыми с помощью Resource Manager, в одной подписке.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: 70fe948070147c01922fab68fb55a0f00c26a0f3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767142"
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Создание пиринга между виртуальными сетями, развернутыми с помощью Resource Manager в одной подписке

В этом руководстве вы узнаете, как установить пиринг между виртуальными сетями, развернутыми с помощью Resource Manager. Обе виртуальные сети относятся к одной и той же подписке. Установление пиринга между двумя виртуальными сетями позволяет ресурсам в разных виртуальных сетях взаимодействовать друг с другом с такими же пропускной способностью и задержкой, как если бы эти ресурсы находились в одной виртуальной сети. Узнайте больше о [пиринге виртуальных сетей](virtual-network-peering-overview.md). 

Действия по созданию пиринга виртуальных сетей зависят от того, находятся ли виртуальные сети в одной или разных подписках, и того, какая [модель развертывания Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) использовалась для их создания. Узнайте, как создать пиринг виртуальных сетей в других сценариях, щелкнув сценарий в следующей таблице.

|Модель развертывания Azure  | Подписка Azure.  |
|--------- |---------|
|[Обе Resource Manager](create-peering-different-subscriptions.md) |Разные|
|[Одна виртуальная сеть Resource Manager, одна классическая виртуальная сеть](create-peering-different-deployment-models.md) |Аналогично|
|[Одна виртуальная сеть Resource Manager, одна классическая виртуальная сеть](create-peering-different-deployment-models-subscriptions.md) |Разные|

Невозможно создать пиринг между двумя виртуальными сетями, созданными с помощью классической модели развертывания. Если вам необходимо подключить виртуальные сети, созданные с помощью классической модели развертывания, можно использовать [VPN-шлюз](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. 

Это руководство по созданию пиринга между виртуальными сетями в одном регионе. Возможность установки пиринга между виртуальными сетями в разных регионах сейчас находится в предварительной версии. Выполните шаги в разделе [Регистрация для предварительной версии пиринга между виртуальными сетями в разных регионах](#register), прежде чем совершить попытку создать пиринг между виртуальными сетями в разных регионах (в противном случае пиринг завершится сбоем). Возможность подключения между виртуальными сетями в разных регионах с использованием [VPN-шлюза](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure находится в общем доступе и не требует регистрации.

Для создания пиринга виртуальных сетей можно использовать [портал Azure](#portal), [интерфейс командной строки](#cli) Azure (CLI), Azure [PowerShell](#powershell) или [шаблон Azure Resource Manager](#template). Щелкните любую из предыдущих ссылок на инструмент, чтобы перейти непосредственно к инструкциям по созданию пиринга виртуальных сетей с помощью выбранного инструмента.

## <a name="portal"></a>Создание пиринга с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com). У учетной записи, используемой для входа, должны быть необходимые разрешения для создания пиринга виртуальных сетей. Дополнительные сведения см. в разделе [Разрешения](#permissions) этой статьи.
2. Щелкните **+ Создать**, выберите **Сети** и щелкните **Виртуальная сеть**.
3. В колонке **Создание виртуальной сети** введите или выберите значения для приведенных ниже параметров и щелкните **Создать**.
    - **Имя**: *myVnet1*.
    - **Адресное пространство**: *10.0.0.0/16*.
    - **Имя подсети**: *по умолчанию*.
    - **Диапазон адресов подсети**: *10.0.0.0/24*.
    - **Подписка**: выберите свою подписку.
    - **Группа ресурсов**: установите флажок **Создать** и введите *myResourceGroup*.
    - **Расположение**: *Восточная часть США*.
4. Повторите шаги 2–3, указав следующие значения на шаге 3:
    - **Имя**: *myVnet2*.
    - **Адресное пространство**: *10.1.0.0/16*.
    - **Имя подсети**: *по умолчанию*.
    - **Диапазон адресов подсети:** *10.1.0.0/24*.
    - **Подписка**: выберите свою подписку.
    - **Группа ресурсов**: установите флажок **Использовать существующий** и выберите *myResourceGroup*.
    - **Расположение**: *Восточная часть США*.
5. В поле **Поиск ресурсов**, находящемся в верхней части портала, введите *myResourceGroup*. Когда группа ресурсов **myResourceGroup** появится в результатах поиска, щелкните ее. Откроется колонка группы ресурсов **myResourceGroup**. Эта группа ресурсов содержит две виртуальные сети, созданные на предыдущих шагах.
6. Щелкните сеть **myVNet1**.
7. В вертикальном списке параметров в левой части отобразившейся колонки **myVnet1** щелкните **Пиринги**.
8. В открывшейся колонке **myVnet1 — пиринги** щелкните **+ Добавить**.
9. В открывшейся колонке **Добавить пиринг** введите или выберите значения приведенных ниже параметров, после чего нажмите кнопку **ОК**.
     - **Имя**: *myVnet1ToMyVnet2*.
     - **Модель развертывания виртуальной сети**: выберите **Resource Manager**. 
     - **Подписка**: выберите свою подписку.
     - **Виртуальная сеть**: щелкните **Выбор виртуальной сети**, затем выберите **myVnet2**.
     - **Разрешить доступ к виртуальным сетям**: убедитесь, что выбрано значение **Включено**.
    Другие параметры в этом руководстве не используются. Чтобы узнать о всех параметрах пиринга, прочитайте раздел [Создание, изменение и удаление пиринга в виртуальной сети](virtual-network-manage-peering.md#create-a-peering).
10. После нажатия кнопки **ОК** на предыдущем шаге колонка **Добавить пиринг** закроется, и вы снова увидите колонку **myVnet1 — пиринги**. Через несколько секунд созданный пиринг отобразится в этой колонке. В столбце **Состояние пиринга** для созданного пиринга **myVnet1ToMyVnet2** указано состояние **Инициировано**. Вы установили пиринг из myVnet1 в myVnet2, но теперь это нужно сделать из myVnet2 в myVnet1. Пиринг необходимо создать в обоих направлениях, чтобы ресурсы в виртуальных сетях могли взаимодействовать друг с другом.
11. Выполните шаги 5–10 для myVnet2. Назовите пиринг *myVnet2ToMyVnet1*.
12. Через несколько секунд после нажатия кнопки **ОК** для создания пиринга **myVnet2ToMyVnet1** для myVnet2 он появится в столбце **Состояние пиринга** с состоянием **Подключен**.
13. Выполните шаги 5–7 для myVnet1. Теперь в столбце **Состояние пиринга** для пиринга **myVnet1ToVNet2** также отображается значение **Подключен**. Пиринг будет успешно установлен, когда значение **Подключен** отобразится в столбце **Состояние пиринга** для обеих виртуальных сетей в пиринге.
14. **Необязательно**. Хотя в этом руководстве не рассматривается создание виртуальных машин, можно создать по виртуальной машине в каждой виртуальной сети и подключить их между собой, чтобы проверить связь.
15. **Необязательно**. Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции в разделе [Удаление ресурсов](#delete-portal) этой статьи.

Теперь все ресурсы Azure, созданные в любой из виртуальных сетей, могут взаимодействовать друг с другом, используя свои IP-адреса. Если вы используете разрешение имен Azure по умолчанию для виртуальных сетей, то ресурсы в этих виртуальных сетях не смогут разрешать имена между виртуальными сетями. Если требуется разрешение имен между виртуальными сетями в пиринге, необходимо создать собственный DNS-сервер. Узнайте, как настроить [разрешение имен с помощью собственного DNS-сервера](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Создание пиринга с помощью Azure CLI

Используйте приведенный ниже сценарий.

- Для его работы требуется Azure CLI 2.0.4 или более поздней версии. Чтобы найти версию, выполните команду `az --version`. Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Этот сценарий работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Вместо установки интерфейса командной строки и его зависимостей вы можете использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Нажмите кнопку **Попробовать** в следующем скрипте, чтобы запустить службу Cloud Shell, с помощью которой можно войти в свою учетную запись Azure. Чтобы выполнить скрипт, нажмите кнопку **Скопировать** и вставьте содержимое в Cloud Shell.

1. Создайте группу ресурсов и две виртуальные сети.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Создайте пиринг двумя виртуальными сетями.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. После выполнения сценария просмотрите пиринги для каждой виртуальной сети. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Выполните предыдущую команду еще раз, заменив *myVnet1* на *myVnet2*. В выходных данных обеих команд в столбце **PeeringState** (Состояние пиринга) показано состояние **Connected** (Подключен).

     Теперь все ресурсы Azure, созданные в любой из виртуальных сетей, могут взаимодействовать друг с другом, используя свои IP-адреса. Если вы используете разрешение имен Azure по умолчанию для виртуальных сетей, то ресурсы в этих виртуальных сетях не смогут разрешать имена между виртуальными сетями. Если требуется разрешение имен между виртуальными сетями в пиринге, необходимо создать собственный DNS-сервер. Узнайте, как настроить [разрешение имен с помощью собственного DNS-сервера](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Необязательно**. Хотя в этом руководстве не рассматривается создание виртуальных машин, можно создать по виртуальной машине в каждой виртуальной сети и подключить их между собой, чтобы проверить связь.
5. **Необязательно**. Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции, описанные в разделе [Удаление ресурсов](#delete-cli) этой статьи.


## <a name="powershell"></a>Создание пиринга с помощью PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. Чтобы запустить сеанс PowerShell, нажмите кнопку **Пуск**, введите **powershell**, а затем щелкните **PowerShell**.
3. В PowerShell войдите в Azure, введя команду `login-azurermaccount`. У учетной записи, используемой для входа, должны быть необходимые разрешения для создания пиринга виртуальных сетей. Дополнительные сведения см. в разделе [Разрешения](#permissions) этой статьи.
4. Создайте группу ресурсов и две виртуальные сети. Для выполнения скрипта скопируйте следующий скрипт, вставьте его в PowerShell и нажмите `Enter` после появления последней строки на экране:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Создайте пиринг двумя виртуальными сетями. Скопируйте следующий скрипт, вставьте его в PowerShell и нажмите `Enter` после того, как на экране появится последняя строка:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Чтобы просмотреть подсети для виртуальной сети, скопируйте команду ниже, вставьте ее в PowerShell и нажмите `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Выполните предыдущую команду еще раз, заменив *myVnet1* на *myVnet2*. В выходных данных обеих команд в столбце **PeeringState** (Состояние пиринга) показано состояние **Connected** (Подключен).
7. **Необязательно**. Хотя в этом руководстве не рассматривается создание виртуальных машин, можно создать по виртуальной машине в каждой виртуальной сети и подключить их между собой, чтобы проверить связь.
8. **Необязательно**. Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции, описанные в разделе [Удаление ресурсов](#delete-powershell) этой статьи.

Теперь все ресурсы Azure, созданные в любой из виртуальных сетей, могут взаимодействовать друг с другом, используя свои IP-адреса. Если вы используете разрешение имен Azure по умолчанию для виртуальных сетей, то ресурсы в этих виртуальных сетях не смогут разрешать имена между виртуальными сетями. Если требуется разрешение имен между виртуальными сетями в пиринге, необходимо создать собственный DNS-сервер. Узнайте, как настроить [разрешение имен с помощью собственного DNS-сервера](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Создание пиринга с помощью шаблона Resource Manager

1. В качестве примера используйте шаблон Azure Resource Manager для [создания пиринга виртуальных сетей](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering). Вместе с шаблоном предоставляются инструкции по его развертыванию с помощью портала Azure, PowerShell или Azure CLI. Войдите в один из инструментов, который вы выбрали для развертывания шаблона, используя учетную запись с разрешениями, необходимыми для создания пиринга виртуальных сетей. Дополнительные сведения см. в разделе [Разрешения](#permissions) этой статьи.
2. **Необязательно**. Хотя в этом руководстве не рассматривается создание виртуальных машин, можно создать по виртуальной машине в каждой виртуальной сети и подключить их между собой, чтобы проверить связь.
3. **Необязательно**. Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции в разделе [Удаление ресурсов](#delete) этой статьи. Для этого можно использовать портал Azure, PowerShell или Azure CLI.

## <a name="permissions"></a>Разрешения

У учетных записей, используемых для создания пиринга виртуальных сетей, должна быть необходимая роль или разрешения. Например, при создании пиринга между двумя виртуальными сетями VNet1 и VNet2 вашей учетной записи должны быть назначены следующие минимальные роли или разрешения для каждой виртуальной сети.
    
|Виртуальная сеть|Роль|Разрешения|
|---|---|---|
|VNet1|[Участник сети](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Участник сети](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Подробнее о [встроенных ролях](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) и присвоении разрешений, определенных для [настраиваемых ролей](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (только для Resource Manager).

## <a name="delete"></a>Удаление ресурсов
По завершении работы с этим руководством может потребоваться удалить созданные в его рамках ресурсы, чтобы за их использование не взималась плата. При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

### <a name="delete-portal"></a>Портал Azure

1. В поле поиска на портале введите **myResourceGroup**. В результатах поиска щелкните **myResourceGroup**.
2. В колонке **myResourceGroup** щелкните значок **Удалить**.
3. Чтобы подтвердить удаление, введите **myResourceGroup** в поле **Введите имя группы ресурсов**, а затем щелкните **Удалить**.

### <a name="delete-cli"></a>Интерфейс командной строки Azure

Введите следующую команду:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Введите следующую команду:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="register"></a>Регистрация для предварительной версии пиринга между виртуальными сетями в разных регионах

Функция создания пиринговых связей между виртуальными сетями в одном регионе является общедоступной. Функция пиринга между виртуальными сетями в разных регионах сейчас находится на этапе предварительной версии. Доступные регионы перечислены в разделе [Обновления Azure](https://azure.microsoft.com/en-us/updates/?product=virtual-network). Чтобы создать пиринг между виртуальными сетями в разных регионах, сначала нужно зарегистрироваться для использования предварительной версии, выполнив приведенные ниже действия (в рамках подписки каждой виртуальной сети, для которой требуется пиринг), используя Azure PowerShell или Azure CLI.

### <a name="powershell"></a>PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. Запустите сеанс PowerShell и войдите в Azure с помощью команды `Login-AzureRmAccount`.
3. Зарегистрируйте подписку, в которой находится каждая виртуальная сеть, для которой нужно создать пиринг, чтобы использовать предварительную версию, введя следующие команды:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Убедитесь, что регистрация прошла успешно, выполнив следующую команду:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Не выполняйте инструкции в разделах для портала, PowerShell, Azure CLI или шаблона Resource Manager в этой статье, пока значение **RegistrationState** (Состояние регистрации) в выходных данных приведенной выше команды не станет **Registered** (Зарегистрировано) для обеих подписок.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

1. [Установить и настроить Azure CLI](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Убедитесь, вы используете Azure CLI версии 2.0.18 или более поздней, введя команду `az --version`. Если это не так, установите последнюю версию.
3. Войдите в Azure с помощью команды `az login`.
4. Зарегистрируйтесь для использования предварительной версии, выполнив следующие команды:

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Убедитесь, что регистрация прошла успешно, выполнив следующую команду:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    Не выполняйте инструкции в разделах для портала, PowerShell, Azure CLI или шаблона Resource Manager в этой статье, пока значение **RegistrationState** (Состояние регистрации) в выходных данных приведенной выше команды не станет **Registered** (Зарегистрировано) для обеих подписок.

## <a name="next-steps"></a>Дополнительная информация

- Внимательно ознакомьтесь с важными [ограничениями и особенностями работы пиринга виртуальных сетей](virtual-network-manage-peering.md#requirements-and-constraints), прежде чем создавать пиринг виртуальных сетей для рабочей среды.
- Узнайте о [параметрах пиринга виртуальных сетей](virtual-network-manage-peering.md#create-a-peering).
- Узнайте, как [создать звездообразную топологию сети](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) с помощью пиринга виртуальных сетей.
