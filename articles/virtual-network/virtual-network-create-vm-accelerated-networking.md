---
title: "Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт"
description: "Сведения о создании виртуальной машины с ускоренной сетью."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: c852a1297261504015a3a985fe14a38957d1a64a
ms.contentlocale: ru-ru
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины с ускоренной сетью

В этом руководстве вы узнаете, как создать виртуальную машину Azure с ускоренной сетью. Ускорение работы в сети — общедоступно для Windows и для определенных дистрибутивов Linux (общедоступная предварительная версия). Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор без этой функции, разгружаются и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

> [!WARNING]
> Эта общедоступная предварительная версия Linux может не отличаться таким же уровнем доступности и надежности, как функции, предоставляемые в режиме общедоступной версии. Эта функция не поддерживается, может иметь ограничения и быть доступной не во всех расположениях Azure. Актуальные сведения о доступности и состоянии этой функции см. на странице обновлений виртуальной сети Azure.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="Limitations"></a>Ограничения
При использовании этой возможности действуют следующие ограничения:

* **Создание сетевого интерфейса.** Функцию ускорения сети можно включить только в новом сетевом интерфейсе. Ее нельзя включить в имеющемся сетевом интерфейсе.
* **Создание виртуальной машины.** Сетевой интерфейс с включенной функцией ускорения сети можно подключить к виртуальной машине при ее создании. Этого нельзя сделать для имеющейся виртуальной машины.
* **Регионы.** Виртуальные машины Windows с ускоренной сетью доступны в большинстве регионов Azure, Виртуальные машины Linux с ускоренной сетью доступны в нескольких регионах. Набор поддерживаемых регионов будет расширяться. Для получения последних сведений см. блог обновлений виртуальной сети Azure.   
* **Поддерживаемые операционные системы.** Windows: Microsoft Windows Server 2012 R2 Datacenter и Windows Server 2016. Linux: Ubuntu Server 16.04 LTS с ядром 4.4.0-77 или более поздней версии, SLES 12 SP2, RHEL 7.3 и CentOS 7.3 (опубликована Rogue Wave Software).
* **Размер виртуальной машины.** Экземпляры общего назначения и оптимизированные для вычислений экземпляры с минимум восемью ядрами. Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Размеры виртуальных машин Linux в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Набор поддерживаемых размеров экземпляров виртуальных машин будет расширяться в будущем.
* **Развертывание только с помощью Azure Resource Manager (ARM).** Ускоренная сеть недоступна для развертывания с помощью ASM/RDFE.

Сведения об изменениях этих ограничений публикуются на [этой странице](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm"></a>Создание виртуальной машины Windows
Виртуальную машину можно создать на портале Azure или с помощью [Azure PowerShell](#windows-powershell).

### <a name="windows-portal"></a>Портал

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. На портале щелкните **+ Создать** > **Вычисления** > **Windows Server 2016 Datacenter**. 
3. В открывшейся колонке **Windows Server 2016 Datacenter** в разделе **Выбор модели развертывания** выберите *Resource Manager* и нажмите кнопку **Создать**.
4. В открывшейся колонке **Основные данные** введите приведенные ниже значения. В остальных полях можно ввести собственные значения или оставить значения по умолчанию. Затем нажмите кнопку **ОК**.

    |Настройка|Значение|
    |---|---|
    |Имя|MyVm|
    |Группа ресурсов|Выберите **Создать** и введите *MyResourceGroup*.|
    |Расположение|Западный регион США 2|

    Если вы еще не работали с Azure, ознакомьтесь со сведениями о [группах ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [подписках](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) и [расположениях](https://azure.microsoft.com/regions) (регионах).
5. В открывшейся колонке **Выбор размера** в текстовом поле **Minimum cores** (Минимальное число ядер) введите *8*, а затем щелкните **Просмотреть все**.
6. Щелкните **DS4_V2 Standard** или любую поддерживаемую виртуальную машину и нажмите кнопку **Выбрать**.
7. В открывшейся колонке **Параметры** оставьте все параметры как есть, но в разделе **Ускоренная сеть** выберите **Включено**, а затем нажмите кнопку **ОК**. **Примечание.** Если на предыдущих шагах вы выбрали размер, операционную систему или расположение виртуальной машины, не указанные в разделе [Ограничения](#Limitations) этой статьи, функция **Ускоренная сеть** отображаться не будет.
8. В открывшейся колонке **Сводка** нажмите кнопку **ОК**. После этого в Azure начнется создание виртуальной машины. Этот процесс займет несколько минут.
9. Чтобы установить драйвер ускорения сети для Windows, выполните действия, описанные в разделе [Настройка виртуальных машин Windows](#configure-windows) этой статьи.

### <a name="windows-powershell"></a>PowerShell
1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) Azure PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Общие сведения об Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Запустите сеанс PowerShell. Для этого в главном меню Windows нажмите кнопку "Пуск", введите в строке **powershell** и в результатах поиска выберите **PowerShell**.
3. В окне PowerShell введите команду `login-azurermaccount`, чтобы войти в систему, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. В браузере скопируйте приведенный ниже сценарий.
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. В окне PowerShell щелкните правой кнопкой мыши, чтобы вставить скрипт, и запустите его выполнение. Появится запрос на ввод имени пользователя и пароль. Используйте эти учетные данные, чтобы войти на виртуальную машину при подключении к ней на следующем шаге. Если выполнение скрипта завершилось сбоем и перед этим вы изменили в нем значения, проверьте, используете ли вы размер, расположение и операционную систему виртуальной машины, указанные в разделе [Ограничения](#Limitations) этой статьи.
6. Чтобы установить драйвер ускорения сети для Windows, выполните действия, описанные в разделе [Настройка виртуальных машин Windows](#configure-windows) этой статьи.

### <a name="configure-windows"></a>Настройка виртуальных машин Windows
После создания виртуальной машины в Azure необходимо установить драйвер ускорения сети для Windows. Перед выполнением следующих действий необходимо создать виртуальную машину Windows с ускоренной сетью, используя указания по созданию с помощью [портала](#windows-portal) или [PowerShell](#windows-powershell), описанные в этой статье. 

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *MyVm*. Когда виртуальная машина **MyVm** появится в результатах поиска, щелкните ее.
3. В открывшейся колонке **MyVm** нажмите кнопку **Подключиться** в верхнем левом углу. **Примечание.** Если под кнопкой **Подключиться** отображается элемент **Создание**, это означает, что виртуальная машина по-прежнему создается в Azure. Вы сможете **подключиться** только после того, как под кнопкой **П** исчезнет элемент **Создание**.
4. Разрешите браузеру скачать файл **MyVm.rdp**,  а затем откройте его. 
5. В открывшемся диалоговом окне **Подключение к удаленному рабочему столу** с сообщением о том, что издателя этого удаленного подключения невозможно определить, нажмите кнопку **Подключиться**.
6. В открывшемся поле **Безопасность Windows** щелкните **Больше вариантов**, а затем выберите **Использовать другую учетную запись**. Введите имя пользователя и пароль, указанный на шаге 4, а затем нажмите кнопку **ОК**.
7. В открывшемся диалоговом окне "Подключение к удаленному рабочему столу" с сообщением, что не удается проверить идентификатор удаленного компьютера, нажмите кнопку **Да**.
8. В главном меню Windows щелкните правой кнопкой мыши кнопку "Пуск" и выберите **Диспетчер устройств**. Разверните узел **Сетевые адаптеры**. Проверьте, есть ли в списке виртуальный адаптер **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**, как показано на следующем рисунке:
   
    ![Диспетчер устройств](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. Ускорение работы в сети теперь включено на вашей виртуальной машине.

## <a name="create-a-linux-vm"></a>Создание виртуальной машины Linux
Виртуальную машину Ubuntu или SLES можно создать на портале Azure или с помощью [Azure PowerShell](#linux-powershell). У виртуальных машин RHEL и CentOS другой рабочий процесс.  Изучите приведенные ниже инструкции.

### <a name="linux-portal"></a>Портал
1. Зарегистрируйтесь на получение предварительной версии функции ускорения сети для Linux, выполнив шаги 1–5 в разделе о [создании виртуальной машины Linux с помощью PowerShell](#linux-powershell).  На портале это сделать невозможно.
2. Выполните шаги 1–8 в разделе о [создании виртуальной машины Windows на портале](#windows-portal) этой статьи. На шаге 2 выберите **Ubuntu Server 16.04 LTS**, а не **Windows Server 2016 Datacenter**. В этом руководстве мы будем использовать пароль, а не ключ SSH. Но при развертываниях в рабочей среде можно использовать любой из этих вариантов. Если по завершении шага 7 из раздела о [создании виртуальной машины Windows на портале](#windows-portal) этой статьи функция **Ускорение сети** так и не отобразилась, это может быть связано со следующим:
    - Вы еще не зарегистрированы на получение предварительной версии функции. Проверьте, **зарегистрировались** ли вы, как описано на шаге 4 в разделе о [создании виртуальной машины Linux с помощью Powershell](#linux-powershell) этой статьи. **Примечание.** Если вы зарегистрировались на получение предварительной версии функции ускорения сети для виртуальных машин Windows (сейчас это делать не нужно), это не распространяется автоматически на виртуальные машины Linux. Чтобы использовать эту функцию на виртуальных машинах Linux, зарегистрируйтесь на ее получение.
    - Вы выбрали операционную систему, расположение и размер виртуальной машины, указанные в разделе [Ограничения](#limitations) этой статьи.
3. Чтобы установить драйвер ускорения сети для Linux, выполните действия, описанные в разделе [Настройка виртуальных машин Linux](#configure-linux) этой статьи.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Если вы создаете виртуальные машины Linux с функцией ускорения сети в подписке, а затем пытаетесь создать виртуальную машину Windows с этой же функцией в той же подписке, этот процесс может завершиться сбоем. Во время действия предварительной версии мы советуем создавать виртуальные машины Windows и Linux с функцией ускорения сети в отдельных подписках.
>

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) Azure PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Общие сведения об Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Запустите сеанс PowerShell. Для этого в главном меню Windows нажмите кнопку "Пуск", введите в строке **powershell** и в результатах поиска выберите **PowerShell**.
3. В окне PowerShell введите команду `login-azurermaccount`, чтобы войти в систему, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Зарегистрируйтесь на получение предварительной версии функции ускорения сети Azure, выполнив следующие действия:
    - Отправьте письмо по адресу [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e), указав идентификатор подписки и цель использования. Дождитесь, пока придет письмо с подтверждением от корпорации Майкрософт о том, что ваша подписка включена.
    - Чтобы проверить регистрацию на получение предварительной версии функции, выполните следующую команду:
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        Не приступайте к шагу 5, пока в выходных данных команды не отобразится состояние **Зарегистрировано**. Выходные данные должны выглядеть так:
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Если вы зарегистрировались на получение предварительной версии функции ускорения сети для виртуальных машин Windows (сейчас это делать не нужно), это автоматически не распространяется на виртуальные машины Linux. Чтобы использовать эту функцию на виртуальных машинах Linux, зарегистрируйтесь на ее получение.
      >
5. В браузере скопируйте следующий скрипт, заменив Ubuntu или SLES по своему усмотрению.  Опять же, рабочий процесс для Redhat и CentOS разный. Он приведен ниже.

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. В окне PowerShell щелкните правой кнопкой мыши, чтобы вставить скрипт, и запустите его выполнение. Появится запрос на ввод имени пользователя и пароль. Используйте эти учетные данные, чтобы войти на виртуальную машину при подключении к ней на следующем шаге. При сбое скрипта убедитесь, что:
    - вы зарегистрированы на получение предварительной версии функции, как описано в шаге 4;
    - измененные перед выполнением скрипта значения размера, расположения и операционной системы виртуальной машины указаны в разделе [Ограничения](#Limitations) этой статьи.
7. Чтобы установить драйвер ускорения сети для Linux, выполните действия, описанные в разделе [Настройка виртуальных машин Linux](#configure-linux) этой статьи.

### <a name="configure-linux"></a>Настройка виртуальных машин Linux

После создания виртуальной машины в Azure необходимо установить драйвер ускорения сети для Linux. Перед выполнением следующих действий необходимо создать виртуальную машину Linux с ускоренной сетью, используя указания по созданию с помощью [портала](#linux-portal) или [PowerShell](#linux-powershell), описанные в этой статье. 

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части портала справа от строки *Поиск ресурсов* щелкните значок **>_**, чтобы запустить Bash Cloud Shell (предварительная версия). После этого в нижней части портала отобразится панель Bash Cloud Shell и через несколько секунд появится командная строка **username@Azure:~$**. Со своего компьютера к виртуальной машине можно подключиться по протоколу SSH, но в рамках этого руководства мы будем использовать Cloud Shell.
3. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *MyVm*. Когда виртуальная машина **MyVm** появится в результатах поиска, щелкните ее.
4. В открывшейся колонке **MyVm** нажмите кнопку **Подключиться** в верхнем левом углу. **Примечание.** Если под кнопкой **Подключиться** отображается элемент **Создание**, это означает, что виртуальная машина по-прежнему создается в Azure. Вы сможете **подключиться** только после того, как под кнопкой **П** исчезнет элемент **Создание**.
5. В Azure откроется окно с запросом на ввод `ssh adminuser@<ipaddress>`. Введите эту команду в Cloud Shell (или скопируйте ее из появившегося на шаге 4 диалогового окна и вставьте в Cloud Shell), а затем нажмите клавишу ВВОД.
6. При появлении запроса на продолжение подключения нажмите кнопку **Да**, а затем нажмите клавишу ВВОД.
7. Введите пароль, использованный при создании виртуальной машины. После успешного входа на виртуальную машину отобразится командная строка adminuser@MyVm:~$. Теперь вы вошли на виртуальную машину через сеанс Cloud Shell. **Примечание.** Срок действия сеанса Cloud Shell истекает после 10 минут бездействия.

На этом этапе инструкции различаются в зависимости от используемого дистрибутива. 

#### <a name="ubuntusles"></a>Ubuntu и SLES

1. В командной строке введите `uname -r` и проверьте версию для:

    * Ubuntu — это версия 4.4.0-77-generic или более поздняя.
    * SLES — это версия 4.4.59-92.20-default или более поздняя.

2. Создайте связь между виртуальным сетевым интерфейсом стандартной сети и виртуальным сетевым интерфейсом ускоренной сети, выполнив команды ниже. Сетевой трафик проходит через виртуальный сетевой интерфейс ускоренной сети, а связь гарантирует, что изменение конфигурации не повлияет на этот трафик.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. После выполнения скрипта виртуальная машина перезапустится через 60 секунд.
4. После перезапуска еще раз подключитесь к виртуальной машине, выполнив шаги 5–7.
5. Выполните команду `ifconfig` и подтвердите, что интерфейс bond0 возобновил работу. 
 
 >[!NOTE]
      >Приложения, использующие функцию ускорения сети, должны взаимодействовать через интерфейс *bond0*, а не *eth0*.  С выходом общедоступной версии функции ускорения сети имя интерфейса может измениться.

#### <a name="rhelcentos"></a>RHEL или CentOS

Создание виртуальных машин Red Hat Enterprise Linux или CentOS 7.3 требует некоторых дополнительных шагов для загрузки последних версий драйверов, необходимых для SR-IOV, и драйвера виртуальной функции для сетевой карты. На первом этапе инструкций подготавливается образ, который можно использовать для создания одной или нескольких виртуальных машин с предварительно загруженными драйверами.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>Первый этап: подготовка базового образа Red Hat Enterprise Linux или CentOS 7.3 

1.  Подготовьте виртуальную машину CentOS 7.3 без SRIOV в Azure.

2.  Установите LIS 4.2.2:
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Скачайте файлы конфигурации.
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Отзовите эту виртуальную машину.

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  С портала Azure остановите эту виртуальную машину, перейдите к ее дискам и запишите универсальный код ресурса (URI) виртуального жесткого диска OSDisk. Этот URI содержит имя виртуального жесткого диска базового образа и его учетную запись хранения. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>Второй этап: подготовка новых виртуальных машин в Azure

1.  Подготовьте новые виртуальные машины на основе New-AzureRMVMConfig, используя виртуальный жесткий диск базового образа, записанный на первом этапе, с включенным ускорением работы в сети на виртуальной сетевой карте:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  После загрузки виртуальных машин проверьте устройство VF, используя команду lspci, и запись Mellanox. Например, в выходных данных lspci должен быть этот элемент:
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  Запустите скрипт соединения:

    ```bash
    sudo bondvf.sh
    ```

4.  Перезагрузите новые виртуальные машины:

    ```bash
    sudo reboot
    ```

Виртуальная машина должна начать работу с настроенным интерфейсом bond0 и включенным путем ускорения работы в сети.  Для подтверждения выполните команду `ifconfig`.

