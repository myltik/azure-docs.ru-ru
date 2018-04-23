---
title: Устранение неполадок, связанных с группами безопасности сети, с помощью PowerShell | Документация Майкрософт
description: Узнайте, как устранять проблемы, вызванные группами безопасности сети, в модели развертывания Azure Resource Manager с помощью Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 6beaeb4b7e5c9e393427d575f1cf8bc48599dbd5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Устранение неполадок, связанных с группами безопасности сети, с помощью PowerShell
> [!div class="op_single_selector"]
> * [портал Azure](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Если вы настроили группы безопасности сети (NSG) на виртуальной машине, но возникают проблемы с подключением к ней, эта статья поможет вам устранить их, так как здесь приводятся возможности диагностики NSG.

NSG позволяют управлять типом трафика, передаваемого в виртуальные машины и из них. NSG можно применять к подсетям в виртуальной сети Azure и сетевым интерфейсам или и к тем, и к другим. Действующие правила сетевого интерфейса — это совокупность правил в NSG, применяемых к сетевому интерфейсу и подсети, к которой он подключен. Правила в этих NSG иногда конфликтуют друг с другом и влияют на возможность сетевого подключения к виртуальной машине.  

Вы можете просмотреть все действующие правила безопасности в NSG, применяемые к сетевым интерфейсам виртуальной машины. В этой статье показано, как устранить проблемы с сетевым подключением к виртуальной машине, используя эти правила в модели развертывания с помощью Azure Resource Manager. Если вы не знакомы с понятиями виртуальной сети и группы безопасности сети, см. обзорные статьи [Обзор виртуальной сети](virtual-networks-overview.md) и [Группа безопасности сети](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Использование действующих правил безопасности для устранения проблем с потоком трафика в виртуальной машине
Сценарий ниже является распространенным примером проблемы с подключением.

Виртуальная машина с именем *VM1* принадлежит к подсети *Subnet1* в виртуальной сети *WestUS-VNet1*. При попытке подключения к виртуальной машине по протоколу RDP через TCP-порт 3389 происходит сбой. И к сетевому интерфейсу *VM1-NIC1*, и к подсети *Subnet1* применены группы безопасности сети. Передача трафика через TCP-порт 3389 разрешена в NSG, связанной с сетевым интерфейсом *VM1-NIC1*, но происходит сбой проверки связи с TCP-портом 3389 виртуальной машины VM1.

В этом примере используется TCP-порт 3389, но описанные ниже шаги можно использовать для анализа сбоев входящего или исходящего подключения через любой порт.

## <a name="detailed-troubleshooting-steps"></a>Подробные инструкции по устранению неполадок
Выполните шаги ниже, чтобы устранить проблемы с NSG для виртуальной машины.

1. Запустите сеанс Azure PowerShell и войдите в Azure. Если вы не знакомы с Azure PowerShell, прочтите статью [Установка и настройка Azure PowerShell](/powershell/azure/overview) . Необходимо назначить вашей учетной записи операцию *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* для сетевого интерфейса. Дополнительные сведения о назначении операций для учетных записей см. в статье [Создание пользовательских ролей для управления доступом на основе ролей в Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Следующая команда возвращает все правила NSG, применяемые к сетевому интерфейсу с именем *VM1-NIC1* в группе ресурсов *RG1*.
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Если вы не знаете имя сетевого интерфейса, введите следующую команду, которая возвращает имена всех сетевых интерфейсов в группе ресурсов. 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    Вот пример возвращаемого списка правил, действующих для сетевого интерфейса *VM1 NIC1* :
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Обратите внимание на следующие сведения в выходных данных.
   
   * Существует два раздела **NetworkSecurityGroup**: один для подсети (*Subnet1*), а другой для сетевого интерфейса (*VM1-NIC1*). В этом примере к обоим разделам применены группы безопасности сети.
   * **Association** показывает, с каким ресурсом (подсетью или сетевым интерфейсом) связана конкретная NSG. Если переместить или отключить ресурс NSG перед выполнением этой команды, такие изменения могут отобразиться в выходных данных команды с задержкой в несколько секунд. 
   * Имена правил, которые начинаются с *defaultSecurityRules*: при создании каждой NSG в ней создаются несколько правил безопасности по умолчанию. Правила по умолчанию нельзя удалить, но они могут быть переопределены правилами с более высоким приоритетом.
     Дополнительные сведения о правилах по умолчанию см. в статье [Группа безопасности сети](virtual-networks-nsg.md#default-rules).
   * **ExpandedAddressPrefix** расшифровывает префиксы адресов для тегов NSG по умолчанию. Каждый тег обозначает несколько префиксов адресов. Расшифровка тегов может быть полезной при устранении неполадок с подключением к виртуальной машине с использованием определенных диапазонов адресов. Например, при использовании пиринга виртуальной сети тег VIRTUAL_NETWORK отображает префиксы одноранговых виртуальных сетей, как представлено в примере выше.
     
     > [!NOTE]
     > Эта команда показывает действующие правила, только если с подсетью и (или) с сетевым интерфейсом связана определенная NSG. Виртуальная машина может иметь несколько сетевых интерфейсов, для которых применяются разные NSG. При устранении неполадок выполните эту команду отдельно для каждого сетевого интерфейса.
     > 
     > 
3. Чтобы было проще разобраться в большом числе правил NSG, введите следующие команды для более подробной диагностики. 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Ниже представлен фильтр для трафика RDP (TCP-порт 3389), примененный к табличному представлению правил:
   
    ![Список правил](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Как вы видите в этой таблице, для протокола RDP одновременно существуют и запрещающее, и разрешающее правила. Выходные данные команды из шага 2 показывают, что правило *DenyRDP* установлено в NSG, примененной к подсети. Для входящего трафика первыми обрабатываются правила, установленные для подсети. Если обнаруживается совпадение, правила NSG для сетевого интерфейса не обрабатываются. В нашем примере правило *DenyRDP* для подсети блокирует трафик по протоколу RDP к виртуальной машине**VM1**.
   
   > [!NOTE]
   > Виртуальная машина может иметь несколько подключенных сетевых интерфейсов. Все они могут быть соединены с разными подсетями. Так как команды, описанные в предыдущих шагах, выполняются для конкретного сетевого интерфейса, важно правильно указать сетевой интерфейс, с которым возникают проблемы. Если вы не уверены, можно выполнить эти команды для каждого сетевого интерфейса, подключенного к виртуальной машине.
   > 
   > 
5. Чтобы подключиться по протоколу RDP к виртуальной машине VM1, измените правило *Запретить RDP (3389)* на правило *Разрешить RDP(3389)* в группе безопасности сети **Subnet1-NSG**. Убедитесь, что TCP-порт 3389 открыт. Для этого установите подключение к виртуальной машине по протоколу RDP или используйте средство PsPing. Дополнительные сведения о PsPing см. на [странице загрузки PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).
   
    Также можно удалять правила из NSG с помощью следующей команды, используя сведения из выходных данных:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Рекомендации
При устранении проблем с подключением необходимо учитывать следующие аспекты:

* Правила NSG по умолчанию будут блокировать входящий доступ из Интернета. Будет разрешен только входящий трафик виртуальной сети. Если вам нужно разрешить доступ из Интернета, следует явным образом добавить соответствующие правила.
* Если ни одно из правил безопасности NSG не является причиной сбоя подключения к виртуальной машине, проблема может быть связана со следующими компонентами:
  * Брандмауэр, запущенный в операционной системе виртуальной машины.
  * Маршруты, настроенные для виртуальных устройств или локального трафика. Интернет-трафик перенаправляется в локальную сеть путем принудительного туннелирования. В этом случае подключение по протоколу RDP или SSH из Интернета к виртуальной машине может не работать (это будет зависеть от того, как оборудование локальной сети обрабатывает трафик). Дополнительные сведения о том, как определять причины проблем с маршрутизацией, которые могут препятствовать поступлению трафика из виртуальной машины или в нее, см. в [этой статье](virtual-network-routes-troubleshoot-powershell.md). 
* Если вы используете одноранговые виртуальные сети, для них тег VIRTUAL_NETWORK по умолчанию автоматически включает префиксы. Эти префиксы можно просмотреть в списке **ExpandedAddressPrefix** , что поможет в диагностике проблем с одноранговыми подключениями к виртуальной сети. 
* Действующие правила безопасности отображаются, только если с сетевым интерфейсом и (или) подсетью виртуальной машины связана определенная группа безопасности сети. 
* Если с сетевым интерфейсом или подсетью виртуальной машины не связана группа NSG и машине назначен общедоступный IP-адрес, все порты будут открыты для входящего и исходящего трафика. Если у виртуальной машины есть общедоступный IP-адрес, настоятельно рекомендуем применить NSG к сетевому интерфейсу или подсети.  

