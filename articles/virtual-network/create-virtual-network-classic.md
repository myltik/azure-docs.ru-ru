---
title: Создание (классической) виртуальной сети Azure с несколькими подсетями | Документация Майкрософт
description: Узнайте, как создать (классическую) виртуальную сеть с несколькими подсетями в Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: a2e2075a735b63588352f3bcdc97e2d6410660fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365429"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Создание (классической) виртуальной сети с несколькими подсетями

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). В этой статье рассматривается использование классической модели развертывания. Для создания виртуальных сетей корпорация Майкрософт рекомендует использовать модель развертывания с помощью [Resource Manager](quick-create-portal.md).

В рамках этого руководства вы узнаете, как создать базовую (классическую) виртуальную сеть Azure с отдельными общедоступной и частной подсетями. Ресурсы Azure, такие как виртуальные машины и облачные службы, можно создавать в подсети. Ресурсы, созданные в (классической) виртуальной сети, могут взаимодействовать друг с другом и с ресурсами в других сетях, подключенных к виртуальной сети.

Дополнительные сведения обо всех параметрах виртуальной сети и подсети см. в [этой](manage-virtual-network.md) и [этой](virtual-network-manage-subnet.md) статье.

> [!WARNING]
> При [отключении подписки](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit) классические виртуальные сети сразу же удаляются Azure независимо от наличия ресурсов. После повторного включения подписки необходимо повторно создать ресурсы, находившиеся в виртуальной сети.

Классическую виртуальную сеть можно создать с помощью [портала Azure](#portal), [командной строки (CLI) Azure 1.0](#azure-cli) или [PowerShell](#powershell).

## <a name="portal"></a>Microsoft Azure

1. С помощью браузера войдите на [портал Azure](https://portal.azure.com). Войдите с использованием [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
2. На портале щелкните **Создать ресурс**.
3. В верхней части открывшейся области *Создать* в поле **Поиск по Marketplace** введите **виртуальная сеть**. Когда в результатах поиска появится пункт **Виртуальная сеть**, щелкните его.
4. В открывшейся области **Виртуальная сеть** в поле **Выбор модели развертывания** выберите **Классический** и нажмите кнопку **Создать**. 
5. В области **Создание виртуальной сети (классической)** введите приведенные ниже значения, а затем щелкните **Создать**.

    |Параметр|Значение|
    |---|---|
    |ИМЯ|myVnet|
    |Пространство адресов|10.0.0.0/16|
    |Имя подсети|Общедоступные|
    |Диапазон адресов подсети|10.0.0.0/24|
    |Группа ресурсов|Оставьте выбранным пункт **Создать**, а затем введите **MyResourceGroup**.|
    |Подписка и расположение|Выберите подписку и расположение.

    Если вы еще не работали с Azure, ознакомьтесь со сведениями о [группах ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [подписках](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) и [расположениях](https://azure.microsoft.com/regions) (которые также называются *регионами*).
4. На портале при создании виртуальной сети можно создать только одну подсеть. В рамках этого руководства вы узнаете, как создать вторую подсеть после создания виртуальной сети. Позже вы можете создать ресурсы, доступные из Интернета, в **общедоступной** подсети. Ресурсы, которые недоступны из Интернета, можно создать в **частной** подсети. Чтобы создать вторую подсеть, введите **myVnet** в поле **Поиск ресурсов** в верхней части страницы. Когда сеть **myVnet** появится в результатах поиска, щелкните ее.
5. В открывшейся области **Создание виртуальной сети (классической)** щелкните **Подсети** (в разделе **Параметры**).
6. В открывшейся области **myVnet — подсети** щелкните **+ Добавить**.
7. В области **Добавление подсети** в поле **Имя** введите **Private**. Для **Диапазон адресов** введите **10.0.1.0/24**.  Последовательно выберите **ОК**.
8. В колонке **myVnet — подсети** отображаются созданные подсети **Public** и **Private**.
9. **Необязательно.** По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата:
    - В области **myVnet** щелкните **Обзор**.
    - В области **myVnet** щелкните значок **Удаление**.
    - В окне **Удалить виртуальную сеть** нажмите кнопку **Да**, чтобы подтвердить удаление.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

1. Вы можете [установить и настроить Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или использовать интерфейс командной строки в Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Для получения справки по командам интерфейса командной строки введите `azure <command> --help`. 
2. В сеансе интерфейса командной строки войдите в Azure, используя команду ниже. Если щелкнуть **Попробуйте!** в поле ниже, откроется Cloud Shell. Вы можете войти в подписку Azure, не вводя следующую команду:

    ```azurecli-interactive
    azure login
    ```

3. Чтобы убедиться, что интерфейс командной строки находится в режиме управления службами, введите следующую команду:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Создайте виртуальную сеть с частной подсетью:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Создайте общедоступную подсеть в виртуальной сети:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Создайте виртуальную сеть и подсети:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Необязательно.** По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Нельзя указать группу ресурсов, в которой будет создана классическая виртуальная сеть, с помощью интерфейса командной строки. Azure создает виртуальную сеть в группе ресурсов с именем *Default-Networking*.

## <a name="powershell"></a>PowerShell

1. Установите последнюю версию модуля [Azure](https://www.powershellgallery.com/packages/Azure) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. Запустите сеанс PowerShell.
3. В PowerShell войдите в Azure, введя команду `Add-AzureAccount`.
4. Измените следующий путь и имя файла (при необходимости), а затем экспортируйте имеющийся файл конфигурации сети:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Для создания виртуальной сети с общедоступной и частной подсетями воспользуйтесь любым текстовым редактором, чтобы добавить следующий элемент **VirtualNetworkSite** в файл конфигурации сети.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Просмотрите полную [схему файла конфигурации сети](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Импортируйте файл конфигурации сети:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Импорт измененного файла конфигурации сети может привести к изменениям в классических виртуальных сетях в вашей подписке. Убедитесь, что вы добавили только указанную выше виртуальную сеть и не изменили или удалили имеющиеся в своей подписке виртуальные сети. 

7. Создайте виртуальную сеть и подсети:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Необязательно.** По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата: Чтобы удалить виртуальную сеть, выполните шаги 4–6, но при этом удалите элемент **VirtualNetworkSite**, добавленный на шаге 5.
 
> [!NOTE]
> Нельзя указать группу ресурсов, в которой будет создана классическая виртуальная сеть, с помощью PowerShell. Azure создает виртуальную сеть в группе ресурсов с именем *Default-Networking*.

---

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о всех параметрах виртуальной сети и подсети см. в статьях [Создание, изменение или удаление виртуальных сетей](manage-virtual-network.md) и [Создание, изменение и удаление подсети в виртуальной сети](virtual-network-manage-subnet.md). Доступны различные варианты использования виртуальных сетей и подсетей в рабочей среде для соответствия различным требованиям.
- Создайте виртуальную машину [с Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [с Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json), а затем подключите ее к имеющейся виртуальной сети.
- Чтобы подключить две виртуальные сети в одном расположении Azure, создайте [пиринг виртуальных сетей](create-peering-different-deployment-models.md) между ними. Вы можете настроить пиринг между виртуальной сетью диспетчера ресурсов и классической виртуальной сетью, но не между двумя классическими виртуальными сетями.
- Чтобы подключить виртуальную сеть к локальной сети, используйте [VPN-шлюз](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или канал [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
