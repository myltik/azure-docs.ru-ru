---
title: "Создание виртуальной сети Azure | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с несколькими подсетями."
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Создание виртуальной сети с несколькими подсетями

В этом руководстве вы узнаете, как создать базовую виртуальную сеть Azure с отдельными общедоступной и частной подсетями. К подсетям можно подключать различные ресурсы Azure, такие как виртуальные машины, облачные службы, среды службы приложений, масштабируемые наборы виртуальных машин, HDInsight и облачные службы. Ресурсы, подключенные к виртуальным сетям, могут взаимодействовать друг с другом через частную сеть Azure.

В последующих разделах приведены инструкции по развертыванию виртуальной сети с помощью [портала Azure](#portal), [интерфейса командной строки](#cli) Azure (Azure CLI), Azure [PowerShell](#powershell) и [шаблона](#template) Azure Resource Manager. Результат использования любого из этих инструментов для развертывания виртуальной сети одинаков. Щелкнув ссылку для любого инструмента, можно перейти к соответствующему разделу статьи. Чтобы узнать больше о всех параметрах виртуальной сети и подсети, прочитайте статьи [Создание, изменение или удаление виртуальных сетей](virtual-network-manage-network.md) и [Создание, изменение и удаление подсетей в виртуальных сетях](virtual-network-manage-subnet.md).

## <a name="portal"></a>Портал Azure

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. На портале щелкните **+ Создать** > **Сети** > **Виртуальная сеть**.
3. В открывшейся колонке **Виртуальная сеть** в поле **Выбор модели развертывания** оставьте выбранным пункт *Resource Manager* и нажмите кнопку **Создать**.
4. В открывшейся колонке **Создание виртуальной сети** введите приведенные ниже значения, после чего нажмите кнопку **Создать**.

    |Настройка|Значение|
    |---|---|
    |Имя|*MyVnet*|
    |Пространство адресов|*10.0.0.0/16*|
    |Имя подсети|Общедоступные|
    |Диапазон адресов подсети|*10.0.0.0/24*|
    |Группа ресурсов|Выберите **Создать** и введите *MyResourceGroup*.|
    |"Subscription and Location" (Подписка и расположение)|Выберите подписку и расположение.

    Если вы еще не работали с Azure, ознакомьтесь со сведениями о [группах ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [подписках](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) и [расположениях](https://azure.microsoft.com/regions) (регионах).
6. При создании виртуальной сети на портале можно создать только одну подсеть. В этом руководстве после создания виртуальной сети создается вторая подсеть. Позже вы можете подключить ресурсы, доступные из Интернета, к *общедоступной* подсети, а ресурсы, которые недоступны из Интернета, — к частной подсети. Чтобы создать вторую подсеть, введите *MyVnet* в поле *Поиск ресурсов* в верхней части портала. Когда сеть **MyVnet** появится в результатах поиска, щелкните ее. При наличии в подписке нескольких виртуальных сетей с одинаковым именем под каждой из них указывается имя группы ресурсов. Щелкните сеть MyVnet с группой ресурсов *MyResourceGroup*.
7. В разделе **Параметры** появившейся колонки **MyVnet** щелкните **Подсети**.
8. В колонке **MyVnet — подсети** щелкните **+Subnet** (+ Подсеть).
9. В колонке **Добавление подсети** в поле **Имя** введите *Private*, в поле **Диапазон адресов** введите *10.0.1.0/24*, затем нажмите кнопку **ОК**.
10. Просмотрите подсети в колонке **MyVnet — подсети**. Вы увидите подсети **Public** и **Private**, которые создали.
11. **Необязательно.** Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции в разделе [Удаление ресурсов](#delete-portal) этой статьи.

## <a name="cli"></a>Интерфейс командной строки
Хотя команды интерфейса командной строки одинаковы для Windows, Linux и macOS, существуют различия в сценариях для оболочек этих операционных систем. Приведенные ниже инструкции предназначены для выполнения сценария Bash, содержащего команды интерфейса командной строки.

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части портала справа от строки *Поиск ресурсов* щелкните значок **>_**, чтобы запустить Azure Cloud Shell для Bash (предварительная версия). После этого в нижней части портала отобразится панель Cloud Shell и через несколько секунд появится командная строка **username@Azure:~$**. Cloud Shell выполняет автоматический вход в Azure с помощью учетных данных, с помощью которых вы прошли аутентификацию на портале.
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
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Создайте файл сценария и сохраните его. В командной строке Cloud Shell введите `nano myscript.sh --nonewlines`. Эта команда запускает редактор GNU nano с открытым в нем пустым файлом myscript.sh. Наведите указатель мыши на окно редактора, щелкните правой кнопкой мыши и выберите **Paste** (Вставить). Содержимое хранилища Cloud Shell не сохраняется между сеансами. Если нужно сохранить сценарий во всех сеансах Cloud Shell, настройте [постоянное хранилище](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) для Cloud Shell. 
5. Нажмите клавиши **CTRL+X**, введите **Y**, а затем нажмите клавишу **ВВОД**, чтобы сохранить файл как myscript.sh.
6. В командной строке Cloud Shell пометьте этот файл как исполняемый, выполнив команду `chmod +x myscript.sh`.
7. Выполните этот сценарий, введя `./myscript.sh`.
8. После завершения выполнения сценария просмотрите подсети виртуальной сети, скопировав приведенную ниже команду и вставив ее в Cloud Shell для Bash.
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Необязательно.** Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции в разделе [Удаление ресурсов](#delete-cli) этой статьи.

## <a name="powershell"></a>PowerShell
1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) Azure PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Общие сведения об Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Запустите сеанс PowerShell. Для этого в главном меню Windows нажмите кнопку "Пуск", введите в строке **powershell** и в результатах поиска выберите **PowerShell**.
3. В окне PowerShell введите команду `login-azurermaccount`, чтобы войти в систему, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. В браузере скопируйте приведенный ниже сценарий.
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Чтобы выполнить сценарий, щелкните правой кнопкой мыши в окне PowerShell.
6. Просмотрите подсети виртуальной сети, скопировав приведенную ниже команду и вставив ее в окно PowerShell.
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Необязательно.** Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции в разделе [Удаление ресурсов](#delete-powershell) этой статьи.

## <a name="template"></a>Шаблон

Виртуальную сеть можно развернуть с помощью шаблона Azure Resource Manager. Дополнительные сведения о шаблонах см. в статье [Общие сведения о диспетчере ресурсов Azure](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Чтобы получить шаблон и ознакомиться с его параметрами, перейдите на веб-страницу [Создание виртуальной сети с двумя подсетями](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Шаблон можно развернуть с помощью [портала](#template-portal), [интерфейса командной строки](#template-cli) или [PowerShell](#template-powershell).

**Необязательно.** Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции в любом из разделов раздела [Удаление ресурсов](#delete) этой статьи.

### <a name="template-portal"></a>Портал

1. В браузере откройте [веб-страницу](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets) шаблона.
2. Нажмите кнопку **Развертывание в Azure**, которая открывает страницу входа портала Azure.
3. Войдите на портал с помощью своей [учетной записи](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Введите следующие значения параметров.

    |Параметр|Значение|
    |---|---|
    |Подписки|Выберите свою подписку.|
    |Группа ресурсов|MyResourceGroup|
    |Расположение|Выберите расположение.|
    |Имя виртуальной сети|MyVnet|
    |Префикс адреса виртуальной сети|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Общедоступные|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. Примите указанные условия и нажмите кнопку **Приобрести**, чтобы развернуть виртуальную сеть.

### <a name="template-cli"></a>Интерфейс командной строки

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части портала справа от строки *Поиск ресурсов* щелкните значок **>_**, чтобы запустить Azure Cloud Shell для Bash (предварительная версия). После этого в нижней части портала отобразится панель Cloud Shell и через несколько секунд появится командная строка **username@Azure:~$**. Cloud Shell выполняет автоматический вход в Azure с помощью учетных данных, с помощью которых вы прошли аутентификацию на портале.
3. Чтобы создать группу ресурсов для виртуальной сети, выполните команду  `az group create --name MyResourceGroup --location eastus`.
4. Шаблон можно развернуть, применив:
    - **Значения параметров по умолчанию**. Введите команду   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`.
    - **Пользовательские значения**. Скачайте и измените шаблон перед его развертыванием. Разверните шаблон, указав параметры в командной строке или воспользовавшись отдельным файлом параметров. Можно скачать файлы шаблона и параметров, нажав кнопку **Browse on GitHub** (Найти на GitHub) на веб-странице [Create a VNet with two subnets template](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Шаблон виртуальной сети с двумя подсетями). На сайте GitHub щелкните **azuredeploy.parameters.json** или **azuredeploy.json**, а затем нажмите кнопку **Raw** (Необработанный) для выбранного файла. Скопируйте содержимое в браузере и сохраните его в файл на компьютере. Измените значения параметров в шаблоне или разверните шаблон с отдельным файлом параметров.  

    Чтобы узнать больше о развертывании шаблонов с помощью этих методов, введите `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) Azure PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Общие сведения об Azure PowerShell](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Запустите сеанс PowerShell. Для этого в главном меню Windows нажмите кнопку "Пуск", введите в строке **powershell** и в результатах поиска выберите **PowerShell**.
3. В окне PowerShell введите команду `login-azurermaccount`, чтобы войти в систему, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Чтобы создать группу ресурсов для виртуальной сети, выполните команду  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`.
5. Шаблон можно развернуть, применив:
    - **Значения параметров по умолчанию**. Введите команду   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`.        
    - **Пользовательские значения**. Скачайте и измените шаблон перед его развертыванием. Разверните шаблон, указав параметры в командной строке или воспользовавшись отдельным файлом параметров. Можно скачать файлы шаблона и параметров, нажав кнопку **Browse on GitHub** (Найти на GitHub) на веб-странице [Create a VNet with two subnets template](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Шаблон виртуальной сети с двумя подсетями). На сайте GitHub щелкните **azuredeploy.parameters.json** или **azuredeploy.json**, а затем нажмите кнопку **Raw** (Необработанный) для выбранного файла. Скопируйте содержимое в браузере и сохраните его в файл на компьютере. Измените значения параметров в шаблоне или разверните шаблон с отдельным файлом параметров.  

    Чтобы узнать больше о развертывании шаблонов с помощью этих методов, введите `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Удаление ресурсов
После изучения этого руководства может потребоваться удалить ресурс, чтобы за его использование не взималась плата. При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

### <a name="delete-portal"></a>Портал

1. На портале в поле *Поиск ресурсов*, находящемся в верхней части портала, начните вводить *MyResourceGroup*. Когда группа ресурсов **MyResourceGroup** появится в результатах поиска, щелкните ее.
2. В появившейся колонке "MyResourceGroup" щелкните значок "Удалить", находящийся в верхней части колонки.
3. Чтобы подтвердить удаление, введите *MyResourceGroup* в поле **Введите имя группы ресурсов** и щелкните **Удалить**.

### <a name="delete-cli"></a>Интерфейс командной строки

В командной строке Cloud Shell введите команду `az group delete --name MyResourceGroup --yes`.

### <a name="delete-powershell"></a>PowerShell

В командной строке PowerShell введите команду `Remove-AzureRmResourceGroup -Name MyResourceGroup`.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы ознакомиться со всеми параметрами виртуальной сети и подсети, прочитайте статьи [Создание, изменение или удаление виртуальных сетей](virtual-network-manage-network.md#view-vnet) и [Создание, изменение и удаление подсетей в виртуальных сетях](virtual-network-manage-subnet.md#create-subnet). Существуют различные параметры, которые позволяют создавать рабочие виртуальные сети и подсети, соответствующие различным требованиям.
- Можно фильтровать входящий и исходящий трафик подсети, создавая и применяя к ней [группы безопасности сети](virtual-networks-nsg.md) (NSG).
- Создайте виртуальную машину [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) и подключите ее к виртуальной сети.
- Подключите виртуальные сети, находящиеся одном расположении, с помощью [пиринга виртуальных сетей](virtual-network-peering-overview.md).
- Подключите виртуальную сеть к локальной сети с использованием [виртуальной частной сети типа "сеть — сеть"](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (VPN) или канала [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

