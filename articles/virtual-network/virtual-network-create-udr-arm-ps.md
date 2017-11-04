---
title: "Создание определяемого пользователем маршрута для направления сетевого трафика через виртуальный сетевой модуль с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создать определяемый пользователем маршрут, чтобы переопределить маршрутизацию по умолчанию Azure, направляя трафик через сетевой виртуальный модуль (NVA)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 4ca7f791b4c5c8bb9020144785b1c1aeb20db195
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Создание определяемого пользователем маршрута с помощью PowerShell

В этом руководстве описывается, как создать определяемые пользователем маршруты для маршрутизации трафика между двумя подсетями [виртуальной сети](virtual-networks-overview.md) через сетевой виртуальный модуль. Сетевой виртуальный модуль — это виртуальная машина, на которой выполняется сетевое приложение, например брандмауэр. Дополнительные сведения о предварительно настроенных сетевых виртуальных модулях, которые можно развернуть в виртуальной сет Azure, см. на странице [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

При создании подсетей в виртуальной сети платформа Azure создает [системные маршруты](virtual-networks-udr-overview.md#system-routes) по умолчанию, которые обеспечивают связь между ресурсами во всех подсетях, как показано на следующем рисунке.

![Маршруты по умолчанию](./media/create-user-defined-route/default-routes.png)

В этом руководстве создается виртуальная сеть с общедоступной и частной подсетями (Public и Private), а также подсетью периметра (DMZ), как показано на рисунке ниже. Обычно веб-серверы развертываются в общедоступной подсети, а сервер приложений или сервер базы данных — в частной подсети. Вы можете создать виртуальную машину в качестве виртуального модуля в подсети периметра и, при необходимости, создать в каждой подсети виртуальную машину, которая обменивается данными и через сетевой виртуальный модуль. Весь трафик между общедоступной и частной подсетями направляется через модуль, как показано на следующем рисунке.

![Определяемые пользователем маршруты](./media/create-user-defined-route/user-defined-routes.png)

Эта статья содержит инструкции по созданию определяемого пользователем маршрута на основе модели развертывания с помощью Resource Manager. Мы рекомендуем использовать эту модель при создании определяемых пользователем маршрутов. Если требуется создать классический определяемый пользователем маршрут, прочитайте раздел [Управление маршрутизацией и использование виртуальных модулей (классический режим) с помощью интерфейса командной строки Azure](virtual-network-create-udr-classic-ps.md). Если вы не знакомы с моделями развертывания Azure, см. статью [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Дополнительные сведения об определяемых пользователем маршрутах см. в статье [Определяемые пользователем маршруты и IP-пересылка](virtual-networks-udr-overview.md#user-defined-routes).

## <a name="create-routes-and-network-virtual-appliance"></a>Создание маршрутов и сетевого виртуального модуля

Можно установить и настроить последнюю версию модуля PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) на компьютере. Или просто нажимайте кнопку **Попробовать** в любом сценарии, чтобы выполнить его в Azure Cloud Shell. В Cloud Shell уже установлен модуль PowerShell AzureRM.
 
1. **Необходимы компоненты**. Создайте виртуальную сеть с двумя подсетями, выполнив действия, описанные в разделе [Создание определяемых пользователем маршрутов с помощью Azure CLI 2.0](#create-a-virtual-network).
2. Если сеанс PowerShell запущен на вашем компьютере, войдите в Azure с использованием своей [учетной записи Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), выполнив команду `login-azurermaccount`. При использовании Cloud Shell вход выполняется автоматически. Может потребоваться перезапустить Cloud Shell, чтобы переключиться с оболочки Bash, использованной при создании необходимой виртуальной сети.
3. Задайте несколько переменных, используемых в инструкциях.

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Создайте подсеть периметра в имеющейся виртуальной сети.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Создайте статический общедоступный IP-адрес для виртуальной машины сетевого виртуального модуля.

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. Создайте виртуальную машину NVA. Это может быть виртуальная машина под управлением операционной системы Windows или Linux. Чтобы создать виртуальную машину, скопируйте сценарий для одной из этих операционных систем и вставьте его в сеанс PowerShell. В случае создания виртуальной машины Windows вставьте сценарий в текстовый редактор, измените значение переменной $cred, а затем вставьте измененный текст в сеанс PowerShell.

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. По умолчанию Azure маршрутизирует трафик между всеми подсетями в виртуальной сети. Создайте маршрут, чтобы изменить маршрутизацию по умолчанию Azure и направить трафик из *общедоступной* подсети в NVA, а не напрямую в *частную* подсеть.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Создайте таблицу маршрутов для *общедоступной* подсети.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. Свяжите эту таблицу маршрутов с общедоступной подсетью. Связывание таблицы маршрутов с подсетью вынуждает Azure направлять весь исходящий трафик подсети в соответствии с маршрутами в этой таблице. Таблицу маршрутов можно связать с несколькими подсетями или не связывать ни с одной из них. С подсетью же можно связать не более одной таблицы маршрутов.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Создайте маршрут для трафика, передаваемого из *частной* подсети в *общедоступную* подсеть через виртуальную машину NVA.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Создайте таблицу маршрутов для *частной* подсети.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Свяжите таблицу маршрутов с *частной* подсетью.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Необязательно**. Создайте виртуальную машину в общедоступной и частной подсетях и проверьте, передается ли трафик между виртуальными машинами через сетевой виртуальный модуль, выполнив действия, описанные в разделе [Проверка маршрутизации](#validate-routing).
15. **Необязательно**. Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции, описанные в разделе [Удаление ресурсов](#delete-resources).

## <a name="validate-routing"></a>Проверка маршрутизации

1. Если это еще не сделано, выполните действия, описанные в разделе [Создание маршрутов и сетевого виртуального модуля](#create-routes-and-network-virtual-appliance).
2. Нажмите кнопку **Попробовать** в поле внизу. Откроется Azure Cloud Shell. При появлении запроса войдите в Azure со своей [учетной записью Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell — это бесплатная оболочка Bash с предварительно установленным интерфейсом командной строки Azure. 

    Приведенные ниже сценарии создают две виртуальные машины, одну в *общедоступной* подсети и одну — в *частной* подсети. Эти сценарии также включают IP-пересылку для сетевого интерфейса в операционной системе NVA, чтобы операционная система могла маршрутизировать трафик через этот сетевой интерфейс. NVA в рабочей среде обычно проверяет трафик перед отправкой, но в этом руководстве простой модуль NVA только направляет трафик, не проверяя его. 

    Нажмите кнопку **Копировать** в сценариях для **Linux** или **Windows**, приведенных ниже, и вставьте их содержимое в текстовый редактор. Измените пароль в переменной *adminPassword*, а затем вставьте сценарий в Azure Cloud Shell. Выполните сценарий для операционной системы, выбранной при создании сетевого виртуального модуля на шаге 7 процедуры [Создание маршрутов и сетевого виртуального модуля](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Проверьте связь между виртуальными машинами в общедоступной и частной подсетях. 

    - Откройте [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm)-подключение (Linux) или подключение [удаленного рабочего стола](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) по общедоступному IP-адресу виртуальной машины *myVm-Public*.
    - В командной строки на виртуальной машине *myVm-Public* введите `ping myVm-Private`. Вы получите ответы, так как NVA направляет трафик из общедоступной подсети в частную подсеть.
    - На виртуальной машине*myVm-Public* выполните команду trace route между виртуальными машинами в общедоступной и частной подсетях. Введите соответствующую команду, приведенную ниже, в зависимости от того, какая операционная система установлена на виртуальных машинах в общедоступной и частной подсетях.
        - **Windows**. В командной строке выполните команду `tracert myvm-private`.
        - **Ubuntu**. Выполните команду `tracepath myvm-private`.
      Трафик проходит через IP-адрес 10.0.2.4 (NVA), прежде чем достигает IP-адреса 10.0.1.4 (виртуальная машина в частной подсети). 
    - Выполните предыдущие шаги, подключившись к виртуальной машине *myVm-Private* и проверив связь с виртуальной машиной *myVm-Public*. Команда trace route показывает, что трафик проходит через IP-адрес 10.0.2.4 (NVA), прежде чем достигает IP-адреса 10.0.0.4 (виртуальная машина в общедоступной подсети).
    - **Необязательно**. Используйте функцию следующего прыжка службы "Наблюдатель за сетями Azure", чтобы проверить следующий прыжок между двумя виртуальными машинами в Azure. Прежде чем использовать службу "Наблюдатель за сетями", необходимо [создать экземпляр службы "Наблюдатель за сетями Azure"](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) для региона, в котором вы хотите его использовать. В этом руководстве используется регион "Восточная часть США". Включив экземпляр службы "Наблюдатель за сетями" в регионе, введите следующую команду, чтобы просмотреть сведения о следующем прыжке между виртуальными машинами в общедоступной и частной подсетях.
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       В выходных данных возвращается параметр **nextHopIpAddress** со значением *10.0.2.4* и параметр **nextHopType** со значением *VirtualAppliance*.

> [!NOTE]
> Чтобы продемонстрировать основные понятия в этом руководстве, виртуальным машинам в общедоступной и частной подсетях назначаются общедоступные IP-адреса и для обеих виртуальных машин разрешен полный доступ к сетевым портам в Azure. При создании виртуальных машин для использования в рабочей среде не следует назначать им общедоступные IP-адреса. Следует фильтровать сетевой трафик для частной подсети, развернув сетевой виртуальный модуль перед ней или назначив группу безопасности сети для подсетей, сетевого интерфейса или для подсетей и сетевого интерфейса. Дополнительные сведения о группах безопасности сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Для выполнения инструкций этого руководства требуется виртуальная сеть с двумя подсетями. Нажмите кнопку **Попробовать** кнопку в поле ниже, чтобы быстро создать виртуальную сеть. Кнопка **Попробовать** открывает [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Хотя в Cloud Shell выполняется оболочка PowerShell или Bash, в этом разделе для создания виртуальной сети используется оболочка Bash. В оболочке Bash установлен интерфейс командной строки Azure. При появлении запроса Cloud Shell войдите в Azure со своей [учетной записью Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p). Чтобы создать виртуальную сеть, используемую в этом руководстве, нажмите кнопку **Копировать** в поле ниже, а затем вставьте сценарий в Azure Cloud Shell.

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Чтобы узнать больше о создании виртуальной сети с помощью портала, PowerShell или шаблона Azure Resource Manager, прочитайте раздел [Создание виртуальной сети с несколькими подсетями](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Удаление ресурсов

По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата: При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней. В PowerShell введите следующую команду.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

- Создайте [высокодоступный сетевой виртуальный модуль](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Сетевые виртуальные модули часто содержат несколько сетевых интерфейсов и используют несколько IP-адресов. Узнайте, как [добавить сетевые интерфейсы в существующую виртуальную машину](virtual-network-network-interface-vm.md#vm-add-nic) и как [добавить IP-адреса в существующий сетевой интерфейс](virtual-network-network-interface-addresses.md#add-ip-addresses). Виртуальные машины любых размеров могут содержать по крайней мере два сетевых интерфейса. Каждый размер виртуальной машины поддерживает определенное максимальное количество сетевых интерфейсов. Чтобы узнать, какое количество сетевых интерфейсов поддерживает каждый размер виртуальной машины, ознакомьтесь с размерами виртуальных машин [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) и [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Создайте определяемый пользователем маршрут, чтобы принудительно направить трафик локально через [VPN-подключение типа "сеть — сеть"](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
