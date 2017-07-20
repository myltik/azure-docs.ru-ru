---
title: "Создание виртуальной сети Azure с подсетями | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с несколькими подсетями в Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: ru-ru
ms.lasthandoff: 06/01/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Создание виртуальной сети с несколькими подсетями

В рамках этого руководства вы узнаете, как создать базовую виртуальную сеть Azure с отдельными общедоступной и частной подсетями. К подсетям можно подключать различные ресурсы Azure, например виртуальные машины, среды службы приложений Azure, масштабируемые наборы виртуальных машин, Azure HDInsight и другие облачные службы. Ресурсы, подключенные к виртуальным сетям, могут взаимодействовать друг с другом через частную сеть Azure.

В следующих разделах приведены шаги по развертыванию виртуальной сети с помощью [портала Azure](#portal), интерфейса командной строки Azure ([Azure CLI](#azure-cli)), [Azure PowerShell](#powershell) и [шаблона Azure Resource Manager](#resource-manager-template). Результат использования любого из этих инструментов для развертывания виртуальной сети одинаков. Щелкните нужную ссылку, чтобы перейти в соответствующий раздел этого руководства. Дополнительные сведения о всех параметрах виртуальной сети и подсети см. в статьях [Создание, изменение или удаление виртуальных сетей](virtual-network-manage-network.md) и [Создание, изменение и удаление подсетей в виртуальных сетях](virtual-network-manage-subnet.md).

## <a name="portal"></a>Портал Azure

1. С помощью браузера войдите на [портал Azure](https://portal.azure.com). Выполните вход с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
2. На портале щелкните **+ Создать** > **Сети** > **Виртуальная сеть**.
3. В открывшейся колонке **Виртуальная сеть** в разделе **Выбор модели развертывания** оставьте выбранным пункт **Диспетчер ресурсов**, а затем щелкните **Создать**.
4. В колонке **Создать виртуальную сеть** введите приведенные ниже значения, а затем щелкните **Создать**:

    |Настройка|Значение|
    |---|---|
    |Имя|MyVnet|
    |Пространство адресов|10.0.0.0/16|
    |Имя подсети|Общедоступные|
    |Диапазон адресов подсети|10.0.0.0/24|
    |Группа ресурсов|Оставьте выбранным пункт **Создать**, а затем введите **MyResourceGroup**.|
    |Подписка и расположение|Выберите подписку и расположение.

    Если вы еще не работали с Azure, ознакомьтесь со сведениями о [группах ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [подписках](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) и [расположениях](https://azure.microsoft.com/regions) (которые также называются *регионами*).
6. На портале при создании виртуальной сети можно создать только одну подсеть. В рамках этого руководства вы узнаете, как создать вторую подсеть после создания виртуальной сети. Позже вы можете подключить ресурсы, доступные из Интернета, к **общедоступной** подсети. Ресурсы, которые недоступны из Интернета, можно подключить к **частной** подсети. Чтобы создать вторую подсеть, введите **MyVnet** в поле **Поиск ресурсов** в верхней части страницы. В результатах поиска щелкните **MyVnet**. Если в подписке есть несколько виртуальных сетей с одним именем, проверьте группы ресурсов, которые отображаются для каждой из них. Удостоверьтесь, что вы выбрали результат поиска **MyVnet**, содержащий группу ресурсов **MyResourceGroup**.
7. В колонке **MyVnet** в разделе **Параметры** щелкните **Подсети**.
8. В колонке **MyVnet — Subnets** (MyVnet — подсети) щелкните **+Subnet** (+ Подсеть).
9. В колонке **Добавление подсети** для **имени** введите **Частное**. Для **Диапазон адресов** введите **10.0.1.0/24**.  Нажмите кнопку **ОК**.
10. Просмотрите подсети в колонке **MyVnet — Subnets** (MyVnet — подсети). Вы увидите созданную **общедоступную** и **частную** подсети.
11. **Необязательно.** Чтобы удалить ресурсы, созданные в рамках этого руководства, выполните инструкции, описанные в разделе по [удалению ресурсов](#delete-portal).

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Команды Azure CLI одинаковы для ОС Windows, Linux или macOS. Тем не менее существуют различия в сценариях между оболочками операционных систем. Приведенные ниже инструкции предназначены для выполнения сценария Bash, содержащего команды интерфейса командной строки Azure:

1. Откройте в браузере [портал Azure](https://portal.azure.com). Выполните вход с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части страницы портала справа от поля **Поиск ресурсов** щелкните значок **>_**, чтобы запустить Azure Cloud Shell для Bash (предварительная версия). В нижней области портала появится панель Cloud Shell. А через несколько секунд появится командная строка **username@Azure:~$**. Cloud Shell выполняет автоматический вход в Azure с помощью учетных данных, использованных для входа на портал.
3. В браузере скопируйте приведенный ниже сценарий.
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Создайте файл сценария и сохраните его. В командной строке Cloud Shell введите `nano myscript.sh --nonewlines`. Эта команда запускает редактор Nano GNU с открытым в нем пустым файлом myscript.sh. Наведите указатель мыши на окно редактора, щелкните правой кнопкой мыши и выберите **Вставить**.  
5. Чтобы сохранить файл как myscript.sh, нажмите сочетание клавиш Ctrl + X, введите **Y**, а затем нажмите клавишу ВВОД. Хранилище Cloud Shell не сохраняет файлы, сохраненные между сеансами. Если нужно сохранить сценарий в сеансах Cloud Shell, настройте [постоянное хранилище](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) для Cloud Shell и сохраните в нем файл.
6. В командной строке Cloud Shell пометьте этот файл как исполняемый, выполнив команду `chmod +x myscript.sh`.
7. Чтобы выполнить сценарий, введите `./myscript.sh`.
8. По завершении выполнения сценария вы можете просмотреть подсети для виртуальной сети. Для этого скопируйте команду ниже, а затем вставьте ее в панель Bash Cloud Shell.
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Необязательно.** Чтобы удалить ресурсы, созданные в рамках этого руководства, выполните инструкции, описанные в разделе по [удалению ресурсов](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. Чтобы запустить сеанс PowerShell, перейдите в **Начало**, введите **powershell**, а затем щелкните **PowerShell**.
3. В окне PowerShell введите `login-azurermaccount`, чтобы выполнить вход с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
4. В браузере скопируйте приведенный ниже сценарий.
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Чтобы выполнить скрипт, щелкните правой кнопкой мыши в окне PowerShell.
6. Чтобы просмотреть подсети для виртуальной сети, скопируйте команду ниже, а затем вставьте ее в окно PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Необязательно.** Чтобы удалить ресурсы, созданные в рамках этого руководства, выполните инструкции, описанные в разделе по [удалению ресурсов](#delete-powershell).

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Вы можете развернуть виртуальную сеть с помощью шаблона Azure Resource Manager. Дополнительные сведения о шаблонах см. в разделе [Развертывание шаблона](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Чтобы получить доступ к шаблону и просмотреть его параметры, ознакомьтесь с шаблоном [создания виртуальной сети с двумя подсетями](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Шаблон можно развернуть с помощью [портала](#template-portal), [Azure CLI](#template-cli) или [PowerShell](#template-powershell).

**Необязательно.** Чтобы удалить ресурсы, созданные в рамках этого руководства, выполните инструкции, описанные в разделе по [удалению ресурсов](#delete).

### <a name="template-portal"></a>Портал Azure

1. В браузере откройте [страницу шаблона](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Нажмите кнопку **Deploy to Azure** (Развернуть в Azure). Откроется страница входа на портале Azure.
3. Войдите на портал с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Введите следующие значения параметров.

    |Параметр|Значение|
    |---|---|
    |Подписки|Выберите свою подписку.|
    |Группа ресурсов|MyResourceGroup|
    |Расположение|Выберите расположение|
    |Имя виртуальной сети|MyVnet|
    |Префикс адреса виртуальной сети|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Общедоступные|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. Примите указанные условия, а затем щелкните **Приобрести**, чтобы развернуть виртуальную сеть.

### <a name="template-cli"></a>Интерфейс командной строки Azure

1. Войдите на [портал](https://portal.azure.com) с помощью своей [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Справа от поля **Поиск ресурсов** щелкните значок **>_**, чтобы запустить Azure Cloud Shell для Bash (предварительная версия). В нижней области портала появится панель Cloud Shell. А через несколько секунд появится командная строка **username@Azure:~$**. Cloud Shell выполняет автоматический вход в Azure с помощью учетных данных, использованных для входа на портал Azure.
3. Чтобы создать группу ресурсов для виртуальной сети, введите следующую команду:  `az group create --name MyResourceGroup --location eastus`
4. Вы можете развернуть шаблон с помощью одного из следующих вариантов параметров:
    - **Значений параметров по умолчанию.** Введите следующую команду:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Настраиваемых значений параметров.** Загрузите и измените шаблон перед его развертыванием. Кроме того, можно развернуть шаблон с помощью параметров командной строки или отдельного файла параметров. Чтобы скачать файлы шаблона и параметров, нажмите кнопку **Browse on GitHub** (Найти на GitHub) на странице шаблона [Create a Virtual Network with two Subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Создание виртуальной сети с двумя подсетями). На сайте GitHub щелкните файл **azuredeploy.parameters.json** или **azuredeploy.json**. А затем нажмите кнопку **Необработанный**, чтобы отобразить файл. Скопируйте содержимое файла в браузере. Сохраните содержимое в файл на компьютере. Вы можете изменить значения параметров в шаблоне или развернуть шаблон с помощью отдельного файла параметров.  

    Чтобы узнать больше о развертывании шаблонов с помощью этих методов, введите `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
2. Чтобы запустить сеанс PowerShell, перейдите в **Начало**, введите **powershell**, а затем щелкните **PowerShell**.
3. В окне PowerShell введите `login-azurermaccount`, чтобы выполнить вход с помощью [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
4. Чтобы создать группу ресурсов для виртуальной сети, введите следующую команду:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Вы можете развернуть шаблон с помощью одного из следующих вариантов параметров:
    - **Значений параметров по умолчанию.** Введите следующую команду:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Настраиваемых значений параметров.** Загрузите и измените шаблон перед его развертыванием. Кроме того, можно развернуть шаблон с помощью параметров командной строки или отдельного файла параметров. Чтобы скачать файлы шаблона и параметров, нажмите кнопку **Browse on GitHub** (Найти на GitHub) на странице шаблона [Create a Virtual Network with two Subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Создание виртуальной сети с двумя подсетями). На сайте GitHub щелкните файл **azuredeploy.parameters.json** или **azuredeploy.json**. А затем нажмите кнопку **Необработанный**, чтобы отобразить файл. Скопируйте содержимое файла в браузере. Сохраните содержимое в файл на компьютере. Вы можете изменить значения параметров в шаблоне или развернуть шаблон с помощью отдельного файла параметров.  

    Чтобы узнать больше о развертывании шаблонов с помощью этих методов, введите `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Удаление ресурсов
По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата. При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

### <a name="delete-portal"></a>Портал Azure

1. В поле поиска на портале введите **MyResourceGroup**. В результатах поиска щелкните **MyResourceGroup**.
2. В колонке **MyResourceGroup** щелкните значок **Удалить**.
3. Чтобы подтвердить удаление, введите **MyResourceGroup** в поле **Введите имя группы ресурсов**, а затем щелкните **Удалить**.

### <a name="delete-cli"></a>Интерфейс командной строки Azure

В командной строке Cloud Shell введите следующую команду: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

В командной строке PowerShell введите следующую команду: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о всех параметрах виртуальной сети и подсети см. в статьях [Создание, изменение или удаление виртуальных сетей](virtual-network-manage-network.md#view-vnet) и [Создание, изменение и удаление подсетей в виртуальных сетях](virtual-network-manage-subnet.md#create-subnet). Доступны различные варианты использования виртуальных сетей и подсетей в рабочей среде для соответствия различным требованиям.
- Вы можете фильтровать входящий и исходящий трафик подсети, создавая и применяя к ней [группы безопасности сети](virtual-networks-nsg.md) (NSG).
- Создайте [виртуальную машину с Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [виртуальную машину с Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), а затем подключите ее к виртуальной сети.
- Подключите виртуальную сеть к другой виртуальной сети в том же расположении, используя [пиринг между виртуальными сетями](virtual-network-peering-overview.md).
- Подключите виртуальную сеть к локальной сети с помощью [виртуальной частной сети типа "сеть — сеть"](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или канала [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

