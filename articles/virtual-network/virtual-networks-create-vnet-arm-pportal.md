---
title: "Создание виртуальной сети Azure с несколькими подсетями | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с несколькими подсетями в Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Создание виртуальной сети с несколькими подсетями

В рамках этого руководства вы узнаете, как создать базовую виртуальную сеть Azure с отдельными общедоступной и частной подсетями. Ресурсы в виртуальной сети могут взаимодействовать друг с другом и с ресурсами в других сетях, подключенных к виртуальной сети. В подсетях можно создавать различные ресурсы Azure, например виртуальные машины, среды службы приложений, масштабируемые наборы виртуальных машин, Azure HDInsigh и облачные службы в одной или разных подсетях в виртуальной сети. Создание ресурсов в разных подсетях дает возможность фильтровать входящий и исходящий сетевой трафик подсетей независимо от [групп безопасности сети](virtual-networks-create-nsg-arm-pportal.md) и [маршрутизировать трафик между подсетями](virtual-network-create-udr-arm-ps.md) через виртуальные сетевые модули, например брандмауэр. 

В следующих разделах приведены шаги по созданию виртуальной сети с помощью [портала Azure](#portal), интерфейса командной строки Azure ([Azure CLI](#azure-cli)), [Azure PowerShell](#powershell) и [шаблона Azure Resource Manager](#resource-manager-template). Результат использования любого из этих инструментов для создания виртуальной сети одинаков. Щелкните нужную ссылку, чтобы перейти в соответствующий раздел этого руководства. Дополнительные сведения обо всех параметрах виртуальной сети и подсети см. в [этой](virtual-network-manage-network.md) и [этой](virtual-network-manage-subnet.md) статье.

Эта статья содержит инструкции по созданию виртуальной сети с использованием модели развертывания с помощью Resource Manager. Мы рекомендуем использовать эту модель при создании виртуальных сетей. Если требуется создать классическую виртуальную сеть, см. [эту статью](create-virtual-network-classic.md). Если вы не знакомы с моделями развертывания Azure, см. статью [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Портал Azure

1. С помощью браузера войдите на [портал Azure](https://portal.azure.com). Войдите с использованием [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
2. На портале щелкните **+ Создать** > **Сети** > **Виртуальная сеть**.
3. В колонке **Создать виртуальную сеть** введите приведенные ниже значения, а затем щелкните **Создать**:

    |Настройка|Значение|
    |---|---|
    |Имя|myVnet|
    |Пространство адресов|10.0.0.0/16|
    |Имя подсети|Общедоступные|
    |Диапазон адресов подсети|10.0.0.0/24|
    |Группа ресурсов|Оставьте выбранным пункт **Создать**, а затем введите **MyResourceGroup**.|
    |Подписка и расположение|Выберите подписку и расположение.

    Если вы еще не работали с Azure, ознакомьтесь со сведениями о [группах ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [подписках](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) и [расположениях](https://azure.microsoft.com/regions) (которые также называются *регионами*).
4. На портале при создании виртуальной сети можно создать только одну подсеть. В рамках этого руководства вы узнаете, как создать вторую подсеть после создания виртуальной сети. Позже вы можете создать ресурсы, доступные из Интернета, в **общедоступной** подсети. Ресурсы, которые недоступны из Интернета, можно создать в **частной** подсети. Чтобы создать вторую подсеть, введите **myVnet** в поле **Поиск ресурсов** в верхней части страницы. В результатах поиска щелкните **myVnet**. Если в подписке есть несколько виртуальных сетей с одним именем, проверьте группы ресурсов, которые отображаются для каждой из них. Удостоверьтесь, что вы выбрали результат поиска **myVnet**, содержащий группу ресурсов **myResourceGroup**.
5. В колонке **myVnet** в разделе **Параметры** щелкните **Подсети**.
6. В колонке **myVnet — подсети** щелкните **+Subnet** (+Подсеть).
7. В колонке **Добавление подсети** для **имени** введите **Частное**. Для **Диапазон адресов** введите **10.0.1.0/24**.  Нажмите кнопку **ОК**.
8. Просмотрите подсети в колонке **myVnet — подсети**. Вы увидите созданную **общедоступную** и **частную** подсети.
9. **Необязательно.** Выполните действия в дополнительных руководствах, перечисленных в разделе [Дальнейшие действия](#next-steps), чтобы отфильтровать входящий и исходящий сетевой трафик каждой подсети с использованием групп безопасности сети, маршрутизировать трафик между подсетями через виртуальный сетевой модуль или подключить виртуальную сеть к другим виртуальным или локальным сетям.
10. **Необязательно.** Удалите ресурсы, созданные в этом руководстве, с помощью шагов, описанных в разделе [Удаление ресурсов](#delete-portal).

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Команды Azure CLI одинаковы для ОС Windows, Linux или macOS. Тем не менее существуют различия в сценариях между оболочками операционных систем. Сценарий ниже выполняется в оболочке Bash. 

1. [Установите и настройте Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Убедитесь, что у вас установлена самая последняя версия Azure CLI. Для получения справки по командам интерфейса командной строки введите `az <command> --help`. Вы можете не устанавливать интерфейс командной строки и его зависимости, а воспользоваться Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Cloud Shell включает предварительно установленный и настроенный интерфейс Azure CLI для использования с вашей учетной записью. Чтобы использовать Cloud Shell, нажмите кнопку Cloud Shell (**>_**) в верхней части [портала](https://portal.azure.com) или просто нажмите кнопку *Попробуйте!* в последующих шагах. 
2. При локальном выполнении интерфейса командной строки войдите в Azure с помощью команды `az login`. При использовании Cloud Shell вход выполняется автоматически.
3. Просмотрите следующий сценарий и комментарии к нему. В браузере скопируйте сценарий и вставьте его в окно сеанса интерфейса командной строки:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. По завершении сценария просмотрите подсети для виртуальной сети. Скопируйте следующую команду и вставьте ее в окно сеанса интерфейса командной строки:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Необязательно.** Выполните действия в дополнительных руководствах, перечисленных в разделе [Дальнейшие действия](#next-steps), чтобы отфильтровать входящий и исходящий сетевой трафик каждой подсети с использованием групп безопасности сети, маршрутизировать трафик между подсетями через виртуальный сетевой модуль или подключить виртуальную сеть к другим виртуальным или локальным сетям.
6. **Необязательно.** Удалите ресурсы, созданные в этом руководстве, с помощью шагов, описанных в разделе [Удаление ресурсов](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. В сеансе PowerShell войдите в Azure с использованием [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) с помощью команды `login-azurermaccount`.

3. Просмотрите следующий сценарий и комментарии к нему. В браузере скопируйте сценарий и вставьте его в окно сеанса PowerShell:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Чтобы просмотреть подсети для виртуальной сети, скопируйте следующую команду, а затем вставьте ее в окно сеанса PowerShell:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Необязательно.** Выполните действия в дополнительных руководствах, перечисленных в разделе [Дальнейшие действия](#next-steps), чтобы отфильтровать входящий и исходящий сетевой трафик каждой подсети с использованием групп безопасности сети, маршрутизировать трафик между подсетями через виртуальный сетевой модуль или подключить виртуальную сеть к другим виртуальным или локальным сетям.
6. **Необязательно.** Удалите ресурсы, созданные в этом руководстве, с помощью шагов, описанных в разделе [Удаление ресурсов](#delete-powershell).

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Вы можете развернуть виртуальную сеть с помощью шаблона Azure Resource Manager. Дополнительные сведения о шаблонах см. в разделе [Развертывание шаблона](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Чтобы получить доступ к шаблону и просмотреть его параметры, ознакомьтесь с шаблоном [создания виртуальной сети с двумя подсетями](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Шаблон можно развернуть с помощью [портала](#template-portal), [Azure CLI](#template-cli) или [PowerShell](#template-powershell).

Действия после развертывания шаблона (необязательные):

1. Прочтите дополнительные руководства, перечисленные в разделе [Дальнейшие действия](#next-steps), чтобы отфильтровать входящий и исходящий сетевой трафик каждой подсети с использованием групп безопасности сети, маршрутизировать трафик между подсетями через виртуальный сетевой модуль или подключить виртуальную сеть к другим виртуальным или локальным сетям.
2. Удалите ресурсы, созданные в этом руководстве, с помощью шагов, описанных в разделе [Удаление ресурсов](#delete).

### <a name="template-portal"></a>Портал Azure

1. В браузере откройте [страницу шаблона](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Нажмите кнопку **Deploy to Azure** (Развернуть в Azure). Если вы еще не вошли в Azure, сделайте это на появившемся экране входа портала Azure.
3. Войдите на портал с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Введите следующие значения параметров.

    |Параметр|Значение|
    |---|---|
    |Подписки|Выберите свою подписку.|
    |Группа ресурсов|myResourceGroup|
    |Расположение|Выберите расположение|
    |Имя виртуальной сети|myVnet|
    |Префикс адреса виртуальной сети|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Общедоступные|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. Примите указанные условия, а затем щелкните **Приобрести**, чтобы развернуть виртуальную сеть.

### <a name="template-cli"></a>Интерфейс командной строки Azure

1. [Установите и настройте Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Убедитесь, что у вас установлена самая последняя версия Azure CLI. Для получения справки по командам интерфейса командной строки введите `az <command> --help`. Вы можете не устанавливать интерфейс командной строки и его зависимости, а воспользоваться Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Cloud Shell включает предварительно установленный и настроенный интерфейс Azure CLI для использования с вашей учетной записью. Чтобы использовать Cloud Shell, нажмите кнопку Cloud Shell, **>_**, в верхней части [портала](https://portal.azure.com) или просто нажмите кнопку **Попробуйте!** в последующих шагах. 
2. При локальном выполнении интерфейса командной строки войдите в Azure с помощью команды `az login`. При использовании Cloud Shell вход выполняется автоматически.
3. Чтобы создать группу ресурсов для виртуальной сети, скопируйте следующую команду и вставьте ее в окно сеанса интерфейса командной строки:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Вы можете развернуть шаблон с помощью одного из следующих вариантов параметров:
    - **Значений параметров по умолчанию.** Введите следующую команду:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Настраиваемых значений параметров.** Загрузите и измените шаблон перед его развертыванием. Кроме того, можно развернуть шаблон с помощью параметров командной строки или отдельного файла параметров. Чтобы скачать файлы шаблона и параметров, нажмите кнопку **Browse on GitHub** (Найти на GitHub) на странице шаблона [Create a Virtual Network with two Subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Создание виртуальной сети с двумя подсетями). На сайте GitHub щелкните файл **azuredeploy.parameters.json** или **azuredeploy.json**. А затем нажмите кнопку **Необработанный**, чтобы отобразить файл. Скопируйте содержимое файла в браузере. Сохраните содержимое в файл на компьютере. Вы можете изменить значения параметров в шаблоне или развернуть шаблон с помощью отдельного файла параметров.  

    Чтобы узнать больше о развертывании шаблонов с помощью этих методов, введите `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. В окне сеанса PowerShell введите `login-azurermaccount`, чтобы выполнить вход с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Чтобы создать группу ресурсов для виртуальной сети, введите следующую команду:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Вы можете развернуть шаблон с помощью одного из следующих вариантов параметров:
    - **Значений параметров по умолчанию.** Введите следующую команду:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Настраиваемых значений параметров.** Загрузите и измените шаблон перед его развертыванием. Кроме того, можно развернуть шаблон с помощью параметров командной строки или отдельного файла параметров. Чтобы скачать файлы шаблона и параметров, нажмите кнопку **Browse on GitHub** (Найти на GitHub) на странице шаблона [Create a Virtual Network with two Subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Создание виртуальной сети с двумя подсетями). На сайте GitHub щелкните файл **azuredeploy.parameters.json** или **azuredeploy.json**. А затем нажмите кнопку **Необработанный**, чтобы отобразить файл. Скопируйте содержимое файла в браузере. Сохраните содержимое в файл на компьютере. Вы можете изменить значения параметров в шаблоне или развернуть шаблон с помощью отдельного файла параметров.  

    Чтобы узнать больше о развертывании шаблонов с помощью этих методов, введите `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Удаление ресурсов

По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата: При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

### <a name="delete-portal"></a>Портал Azure

1. В поле поиска на портале введите **myResourceGroup**. В результатах поиска щелкните **myResourceGroup**.
2. В колонке **myResourceGroup** щелкните значок **Удалить**.
3. Чтобы подтвердить удаление, введите **myResourceGroup** в поле **Введите имя группы ресурсов**, а затем щелкните **Удалить**.

### <a name="delete-cli"></a>Интерфейс командной строки Azure

В окне сеанса интерфейса командной строки введите следующую команду:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

В окне сеанса PowerShell введите следующую команду:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о всех параметрах виртуальной сети и подсети см. в статьях [Создание, изменение или удаление виртуальных сетей](virtual-network-manage-network.md#view-vnet) и [Создание, изменение и удаление подсети в виртуальной сети](virtual-network-manage-subnet.md#create-subnet). Доступны различные варианты использования виртуальных сетей и подсетей в рабочей среде для соответствия различным требованиям.
- Вы можете фильтровать входящий и исходящий трафик подсети, создавая и применяя к ней [группы безопасности сети](virtual-networks-nsg.md).
- Вы можете маршрутизировать трафик между подсетями через виртуальный сетевой модуль, создавая [определяемые пользователем маршруты](virtual-network-create-udr-arm-ps.md) и применяя их к каждой подсети.
- Создайте виртуальную машину с [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) в имеющейся виртуальной сети.
- Подключите две виртуальные сети, установив между ними [пиринг](virtual-network-peering-overview.md).
- Чтобы подключить виртуальную сеть к локальной сети, используйте [VPN-шлюз](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или канал [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

