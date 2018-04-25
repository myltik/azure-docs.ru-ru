---
title: Руководство. Автомасштабирование масштабируемого набора с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как автоматически масштабировать масштабируемый набор виртуальных машин с помощью Azure PowerShell по мере увеличения и уменьшения нагрузки на ЦП
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f184c30f1f39563d6e029d506237e6b0e23ec482
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Руководство. Автоматическое масштабирование масштабируемого набора виртуальных машин с помощью Azure PowerShell
При создании масштабируемого набора вы определяете количество экземпляров виртуальных машин для запуска. По мере изменения потребностей приложения можно автоматически увеличивать или уменьшать это количество. Возможность автоматического масштабирования позволяет удовлетворить пользовательский спрос или среагировать на изменения производительности приложения на протяжении его жизненного цикла. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * автомасштабирование масштабируемого набора;
> * создание и использование правил автомасштабирования;
> * нагрузочное тестирование экземпляров виртуальных машин и активация правил автомасштабирования;
> * обратное автомасштабирование при уменьшении потребности в ресурсах.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, для работы с этим руководством необходима версия модуля Azure PowerShell версии 5.6.0 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Чтобы упростить создание правил автомасштабирования, определите некоторые переменные для масштабируемого набора. В примере ниже определены переменные для масштабируемого набора *myScaleSet* в группе ресурсов с именем *myResourceGroup* в регионе *восточная часть США*. Идентификатор подписки можно получить с помощью командлета [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Если с вашей учетной записью связано несколько подписок, вернется только первая подписка. Измените имена и идентификатор подписки, как показано ниже:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Настройте имя и пароль администратора для экземпляров виртуальных машин с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Теперь создайте масштабируемый набор виртуальных машин с помощью командлета [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Чтобы распределить трафик между отдельными экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Подсистема балансировки нагрузки определяет правила передачи трафика на TCP-порт 80, а также разрешает подключение удаленного рабочего стола трафик через TCP-порт 3389 и удаленное взаимодействие PowerShell через TCP-порт 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.

## <a name="create-a-rule-to-autoscale-out"></a>Создание правила для автоматического увеличения масштаба
При увеличении потребностей приложения в масштабируемом наборе увеличивается нагрузка на экземпляры виртуальной машины. Если такая увеличенная нагрузка представляет собой не просто краткий спрос, а является согласованной, можно настроить правила автомасштабирования, чтобы увеличить число экземпляров виртуальной машины в масштабируемом наборе. После создания этих экземпляров виртуальных машин и развертывания приложений масштабируемые наборы запускают распределение трафика между ними через подсистему балансировки нагрузки. Вы можете контролировать, какие метрики отслеживать, например ЦП или диск, продолжительность соответствия нагрузки приложения заданному пороговому значению, а также количество экземпляров виртуальной машины для добавления в масштабируемый набор.

Используя командлет [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule), создадим правило, в соответствии с которым количество экземпляров виртуальной машины в масштабируемом наборе увеличивается, когда средняя загрузка ЦП превышает 70 % в течение 5 минут. При активации правила количество экземпляров виртуальных машин увеличивается до трех.

Ниже приведены параметры, используемые для этого правила.

| Параметр               | Пояснение                                                                                                         | Значение          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Метрика производительности для мониторинга и применения действий в масштабируемом наборе.                                                   | Percentage CPU |
| *-TimeGrain*            | Определяет, как часто собираются показатели для анализа.                                                                   | 1 минута       |
| *-MetricStatistic*      | Определяет способ вычисления собранных метрик для анализа.                                                | Средняя        |
| *-TimeWindow*           | Количество времени, в течение которого выполняется отслеживание перед сравнением значений метрик и пороговых значений.                                   | 5 мин      |
| *-Operator*             | Оператор для сравнения данных метрики с пороговым значением.                                                     | Больше чем   |
| *-Threshold*            | Значение, при достижении которого правило автомасштабирования запускает действие.                                                      | 70 %            |
| *-ScaleActionDirection* | Определяет действие, применяемое к масштабируемому набору при выполнении условий правила: увеличение или уменьшение числа экземпляров.                                             | Увеличить       |
| *-ScaleActionScaleType* | Указывает, что количество экземпляров виртуальных машин необходимо изменить, использовав определенное значение.                                    | Изменение количества   |
| *-ScaleActionValue*     | При активации правила должен измениться процент экземпляров виртуальных машин.                                            | 3              |
| *-ScaleActionCooldown*  | Время ожидания перед повторным применением правила, чтобы обеспечить время для активации действий автомасштабирования. | 5 мин      |

В следующем примере создается объект с именем *myRuleScaleOut*, содержащий это правило увеличения масштаба. *-MetricResourceId* использует переменные, определенные ранее для идентификатора подписки, имени группы ресурсов и имени масштабируемого набора:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Создание правила для автоматического уменьшения масштаба
В вечерние часы или выходные дни потребность в приложении может снизиться. Если такая сниженная нагрузка является согласованной в течение некоторого периода времени, можно настроить правила автомасштабирования, чтобы уменьшить число экземпляров виртуальных машин в масштабируемом наборе. Это действие снижает стоимость запуска масштабируемого набора, так как вы запускаете только то количество экземпляров, которое необходимо для удовлетворения текущего спроса.

Создайте другое правило, используя командлет [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule), в соответствии с которым количество экземпляров виртуальной машины в масштабируемом наборе уменьшается, когда средняя загрузка ЦП не превышает 30 % в течение 5 минут. При активации правила количество экземпляров виртуальных машин уменьшается на одну. В следующем примере создается объект с именем *myRuleScaleDown*, содержащий это правило увеличения масштаба. *-MetricResourceId* использует переменные, определенные ранее для идентификатора подписки, имени группы ресурсов и имени масштабируемого набора:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Определение профиля автомасштабирования
Чтобы сопоставить правила автомасштабирования с масштабируемым набором, необходимо создать профиль. Профиль автомасштабирования определяет минимальную и максимальную емкость масштабируемого набора, а также емкость по умолчанию и сопоставляет правила автомасштабирования. Создайте профиль автомасштабирования с помощью командлета [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). В примере ниже задаются минимальная и максимальная емкости (*2* и *10*), а также емкость по умолчанию экземпляров виртуальной машины. Затем применяются правила увеличения и уменьшения масштаба, созданные ранее.

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Применение правил автомасштабирования к масштабируемому набору
Остается применить профиль автомасштабирования к масштабируемому набору. После этого станет возможным автоматическое добавление и удаление экземпляров в зависимости от потребностей приложения. Примените профиль автомасштабирования с помощью командлета [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting), как показано ниже:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Создание нагрузки на ЦП в масштабируемом наборе
Чтобы проверить правила автомасштабирования, создайте нагрузку на ЦП в экземплярах виртуальных машин в масштабируемом наборе. В результате этой имитации нагрузки на ЦП происходит автомасштабирование и увеличение числа экземпляров виртуальных машин. Когда имитированная нагрузка на ЦП снижается, в соответствии с правилами автомасштабирования выполняется уменьшение числа экземпляров виртуальных машин.

Чтобы получить список портов NAT для подключения к экземплярам виртуальных машин в масштабируемом наборе, сначала получите объект подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Затем просмотрите правила преобразования сетевых адресов для входящих подключений с помощью командлета [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig).

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

В следующем примере выходных данных показано имя экземпляра, общедоступный IP-адрес подсистемы балансировки нагрузки и номер порта, в который правила преобразования сетевых адресов перенаправляют трафик.

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Как показано с помощью предыдущей команды [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm), *имя* правила соответствует имени экземпляра виртуальной машины. Например, чтобы подключиться к экземпляру виртуальной машины *0*, используйте *myRDPRule.0* и подключитесь к порту *50001*. Для подключения к экземпляру виртуальной машины *1* используйте значение из *myRDPRule.1* и подключитесь к порту *50002*.

Получите сведения об общедоступном IP-адресе подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress).

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Выходные данные примера:

```powershell
IpAddress
---------
52.168.121.216
```

Создайте удаленное подключение к первому экземпляру виртуальной машины. Укажите собственный общедоступный IP-адрес и номер порта требуемого экземпляра виртуальной машины, как показано в предыдущих командах. При появлении запроса введите учетные данные, используемые для создания масштабируемого набора (по умолчанию в используемых командах этими значениями являются *azureuser* и *P@ssw0rd!*). Если вы используете Azure Cloud Shell, выполните этот шаг с помощью локальной командной строки PowerShell или клиента удаленного рабочего стола. В следующем примере устанавливается подключение к экземпляру виртуальной машины *0*.

```powershell
mstsc /v 52.168.121.216:50001
```

После входа в систему на панели задач откройте Internet Explorer.

- Нажмите кнопку **ОК**, чтобы принять приглашение об *использовании рекомендуемых параметров безопасности, конфиденциальности и совместимости*.
- В адресной строке введите *http://download.sysinternals.com/files/CPUSTRES.zip*.
- Так как конфигурация усиленной безопасности Internet Explorer включена, **добавьте** домен *http://download.sysinternals.com* в список надежных сайтов.
- При появлении запроса на загрузку файла выберите **Открыть**, а затем выберите инструмент *CPUSTRES.EXE* и **запустите его**.

Чтобы создать нагрузку на ЦП, установите для двух потоков флажки **Active** (Активен). В раскрывающемся меню **Activity** (Действие) для обоих потоков выберите вариант *Maximum* (Максимальное). Чтобы убедиться, что загрузка ЦП на виртуальной машине достигла 100 %, можно открыть диспетчер задач.

![Служебная программа CPU Stress создает нагрузку на экземпляре виртуальной машины](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Оставьте сеанс подключения к удаленному рабочему столу открытым и откройте другой такой сеанс. Подключитесь к другому экземпляру виртуальной машины, используя номер порта, указанный в выходных данных предыдущего командлета [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig).

```powershell
mstsc /v 52.168.121.216:50002
```

После входа в другой экземпляр виртуальной машины повторите предыдущие шаги для загрузки и запуска *CPUSTRES.EXE*. Снова установите флажок **Active** (Активный) для двух потоков и выберите значение действия *Maximum* (Максимальное).

Чтобы позволить инструменту **CPU Stress** продолжать работу, оставьте оба сеанса подключения к удаленному рабочему столу открытыми.


## <a name="monitor-the-active-autoscale-rules"></a>Мониторинг активных правил автомасштабирования
Чтобы отслеживать количество экземпляров виртуальных машин в масштабируемом наборе, используйте **while**. Потребуется 5 минут, чтобы в соответствии с правилами автомасштабирования началось развертывание с учетом нагрузки на ЦП, созданной программой **CPUStress* на каждом экземпляре виртуальной машины.

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Когда достигается порог нагрузки на ЦП, в соответствии с правилами автомасштабирования увеличивается число экземпляров виртуальных машин в масштабируемом наборе. В следующих выходных данных показано, что при автомасштабировании масштабируемого набора создано три виртуальные машины.

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

В сеансе подключения к удаленному рабочему столу каждого экземпляра виртуальной машины закройте инструмент **CPU Stress**. Средняя нагрузка ЦП по всему масштабируемому набору возвращается к норме. На протяжении следующих 5 минут в соответствии с правилами автомасштабирования уменьшается число экземпляров виртуальных машин. При таком свертывании сначала удаляются экземпляры виртуальных машин с более высокими значениями идентификатора. Если в масштабируемом наборе используются группы доступности или зоны доступности, действия свертывания равномерно распределяются между экземплярами виртуальных машин. В следующем примере выходных данных показано удаление одного экземпляра виртуальной машины при автоматическом уменьшении масштаба масштабируемого набора:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Выйти из *while* можно с помощью клавиш `Ctrl-c`. Каждые 5 минут будет происходить уменьшение масштаба масштабируемого набора с удалением одного экземпляра виртуальной машины, пока не будет достигнуто минимальное число экземпляров (2 экземпляра).


## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы удалить масштабируемый набор и дополнительные ресурсы, удалите группу ресурсов и все входящие в нее ресурсы с помощью команды [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Параметр `-Force` подтверждает, что вы хотите удалить ресурсы без дополнительного запроса. При использовании параметра `-AsJob` управление возвращается в командную строку без ожидания завершения операции.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнять автомасштабирование масштабируемого набора с помощью Azure PowerShell.

> [!div class="checklist"]
> * автомасштабирование масштабируемого набора;
> * создание и использование правил автомасштабирования;
> * нагрузочное тестирование экземпляров виртуальных машин и активация правил автомасштабирования;
> * обратное автомасштабирование при уменьшении потребности в ресурсах.

Дополнительные примеры использования масштабируемых наборов виртуальных машин см. в следующих примерах скриптов для Azure PowerShell.

> [!div class="nextstepaction"]
> [Scale set script samples for Azure PowerShell](powershell-samples.md) (Примеры Azure PowerShell для масштабируемых наборов виртуальных машин)
