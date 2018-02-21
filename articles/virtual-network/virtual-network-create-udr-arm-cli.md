---
title: "Создание определяемого пользователем маршрута для направления сетевого трафика через виртуальный сетевой модуль с помощью Azure CLI | Документация Майкрософт"
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
ms.openlocfilehash: adfcf53f9fca0efafb538edfd65b95313dcf1559
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>Создание определяемого пользователем маршрута с помощью Azure CLI

В этом руководстве описывается, как создать определяемые пользователем маршруты для маршрутизации трафика между двумя подсетями [виртуальной сети](virtual-networks-overview.md) через сетевой виртуальный модуль. Сетевой виртуальный модуль — это виртуальная машина, на которой выполняется сетевое приложение, например брандмауэр. Дополнительные сведения о предварительно настроенных сетевых виртуальных модулях, которые можно развернуть в виртуальной сет Azure, см. на странице [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

При создании подсетей в виртуальной сети платформа Azure создает [системные маршруты](virtual-networks-udr-overview.md#system-routes) по умолчанию, которые обеспечивают связь между ресурсами во всех подсетях, как показано на следующем рисунке.

![Маршруты по умолчанию](./media/create-user-defined-route/default-routes.png)

В этом руководстве создается виртуальная сеть с общедоступной и частной подсетями (Public и Private), а также подсетью периметра (DMZ), как показано на рисунке ниже. Обычно веб-серверы развертываются в общедоступной подсети, а сервер приложений или сервер базы данных — в частной подсети. Вы можете создать виртуальную машину в качестве виртуального модуля в подсети периметра и, при необходимости, создать в каждой подсети виртуальную машину, которая обменивается данными и через сетевой виртуальный модуль. Весь трафик между общедоступной и частной подсетями направляется через модуль, как показано на следующем рисунке.

![Определяемые пользователем маршруты](./media/create-user-defined-route/user-defined-routes.png)

Эта статья содержит инструкции по созданию определяемого пользователем маршрута на основе модели развертывания с помощью Resource Manager. Мы рекомендуем использовать эту модель при создании определяемых пользователем маршрутов. Если требуется создать классический определяемый пользователем маршрут, прочитайте раздел [Управление маршрутизацией и использование виртуальных модулей (классический режим) с помощью интерфейса командной строки Azure](virtual-network-create-udr-classic-cli.md). Если вы не знакомы с моделями развертывания Azure, см. статью [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Дополнительные сведения об определяемых пользователем маршрутах см. в статье [Определяемые пользователем маршруты и IP-пересылка](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Создание маршрутов и сетевого виртуального модуля

Команды Azure CLI одинаковы для ОС Windows, Linux или macOS. Тем не менее существуют различия в сценариях между оболочками операционных систем. Сценарии в следующих инструкциях выполняются для обязательной установки и выполнения команд Azure CLI в оболочке Bash. Можно [установить и настроить Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) на компьютере. Или просто нажимайте кнопку **Попробовать** в любом сценарии, чтобы выполнить его в Azure Cloud Shell.
 
1. **Необходимы компоненты**. Создайте виртуальную сеть с двумя подсетями, выполнив действия, описанные в разделе [Создание определяемых пользователем маршрутов с помощью Azure CLI 2.0](#create-a-virtual-network).
2. Если вы запустили Azure CLI на компьютере, войдите в Azure с помощью команды `az login`. При использовании Cloud Shell вход выполняется автоматически.
3. Задайте несколько переменных, используемых в оставшихся шагах.

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. Создайте подсеть *периметра* (DMZ) в виртуальной сети, созданной при выполнении предварительных условий.

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. Создайте виртуальную машину NVA. Назначьте статические общедоступный и частный IP-адреса сетевому интерфейсу, созданному интерфейсом командной строки. Статические адреса не изменяются в течение жизненного цикла виртуальной машины. В роли NVA может выступать виртуальная машина под управлением операционной системы Windows или Linux. Чтобы создать виртуальную машину, скопируйте сценарий для одной из этих операционных систем и вставьте его в окно интерфейса командной строки. В случае создания виртуальной машины Windows вставьте сценарий в текстовый редактор, измените значение переменной *AdminPassword*, а затем вставьте измененный текст в окно интерфейса командной строки.

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. Включите IP-пересылку для сетевого интерфейса NVA. После включения IP-пересылки для сетевого интерфейса платформа Azure не будет проверять IP-адреса источника и назначения. Если не включить этот параметр, то трафик, предназначенный для IP-адреса, отличного от адреса сетевой карты, которая его получает, будет удаляться платформой Azure.

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. Создайте таблицу маршрутов для *общедоступной* подсети.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. По умолчанию Azure маршрутизирует трафик между всеми подсетями в виртуальной сети. Создайте маршрут, чтобы изменить маршрутизацию по умолчанию Azure и направить трафик из общедоступной подсети поступал в частную подсеть через NVA, а не напрямую.

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. Свяжите таблицу маршрутов *myRouteTable-Public* с *общедоступной* подсетью. Связывание таблицы маршрутов с подсетью вынуждает Azure направлять весь исходящий трафик подсети в соответствии с маршрутами в этой таблице. Таблицу маршрутов можно связать с несколькими подсетями или не связывать ни с одной из них. С подсетью же можно связать не более одной таблицы маршрутов.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. Создайте таблицу маршрутов для *частной* подсети.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. Создайте маршрут для передачи трафика из *частной* подсети в *общедоступную* подсеть через виртуальную машину NVA.

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. Свяжите таблицу маршрутов с *частной* подсетью.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **Необязательно**. Создайте виртуальную машину в общедоступной и частной подсетях и проверьте, передается ли трафик между виртуальными машинами через сетевой виртуальный модуль, выполнив действия, описанные в разделе [Проверка маршрутизации](#validate-routing).
14. **Необязательно**. Чтобы удалить ресурсы, созданные в этом руководстве, выполните инструкции, описанные в разделе [Удаление ресурсов](#delete-resources).

## <a name="validate-routing"></a>Проверка маршрутизации

1. Если это еще не сделано, выполните действия, описанные в разделе [Создание маршрутов и сетевого виртуального модуля](#create-routes-and-network-virtual-appliance).
2. Нажмите кнопку **Попробовать** в поле внизу. Откроется Azure Cloud Shell. При появлении запроса войдите в Azure со своей [учетной записью Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell — это бесплатная оболочка Bash с предварительно установленным интерфейсом командной строки Azure. 

    Приведенные ниже сценарии создают две виртуальные машины, одну в *общедоступной* подсети и одну — в *частной* подсети. Эти сценарии также включают IP-пересылку для сетевого интерфейса в операционной системе NVA, чтобы операционная система могла маршрутизировать трафик через этот сетевой интерфейс. NVA в рабочей среде обычно проверяет трафик перед отправкой, но в этом руководстве простой модуль NVA только направляет трафик, не проверяя его. 

    Нажмите кнопку **Копировать** в сценариях для **Linux** или **Windows**, приведенных ниже, и вставьте их содержимое в текстовый редактор. Измените пароль в переменной *adminPassword*, а затем вставьте сценарий в Azure Cloud Shell. Выполните сценарий для операционной системы, выбранной при создании сетевого виртуального модуля на шаге 5 процедуры [Создание маршрутов и сетевого виртуального модуля](#create-routes-and-network-virtual-appliance). 

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
      
      > [!NOTE]
      > Приведенные выше действия позволяют проверить маршрутизацию между частными IP-адресами Azure. Если вы хотите пересылать трафик или передавать его через прокси-сервер на общедоступные IP-адреса с помощью сетевого виртуального модуля, сделайте вот что:
      > - Убедитесь, что модуль преобразовывает сетевые адреса или имеет функциональные возможности прокси-сервера. В случае с преобразованием сетевых адресов модуль должен преобразовывать исходный IP-адрес в свой собственный, а затем пересылать запрос на общедоступный IP-адрес. Azure преобразовывает частный IP-адрес сетевого виртуального модуля в общедоступный IP-адрес в любом случае: и при преобразовании модулем исходных сетевых адресов, и при работе модуля в роли прокси-сервера. Дополнительные сведения о том, как Azure преобразовывает частные IP-адреса в общедоступные, см. в статье [Общие сведения об исходящих подключениях в Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Добавьте в таблицу маршрутов дополнительный маршрут, например префикс 0.0.0.0/0, тип следующего прыжка VirtualAppliance и IP-адрес для следующего прыжка 10.0.2.4 (см. пример сценария выше).
      >
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

## <a name="delete-resources"></a>Удаление ресурсов.

По завершении работы с этим руководством может потребоваться удалить созданные ресурсы, чтобы за их использование не взималась плата: При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней. В окне сеанса интерфейса командной строки введите следующую команду:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

- Создайте [высокодоступный сетевой виртуальный модуль](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Сетевые виртуальные модули часто содержат несколько сетевых интерфейсов и используют несколько IP-адресов. Узнайте, как [добавить сетевые интерфейсы в существующую виртуальную машину](virtual-network-network-interface-vm.md#vm-add-nic) и как [добавить IP-адреса в существующий сетевой интерфейс](virtual-network-network-interface-addresses.md#add-ip-addresses). Виртуальные машины любых размеров могут содержать по крайней мере два сетевых интерфейса. Каждый размер виртуальной машины поддерживает определенное максимальное количество сетевых интерфейсов. Чтобы узнать, какое количество сетевых интерфейсов поддерживает каждый размер виртуальной машины, ознакомьтесь с размерами виртуальных машин [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) и [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Создайте определяемый пользователем маршрут, чтобы принудительно направить трафик локально через [VPN-подключение типа "сеть — сеть"](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
