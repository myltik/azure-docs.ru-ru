---
title: Миграция в Resource Manager с помощью PowerShell | Документация Майкрософт
description: В этой статье последовательно описывается поддерживаемый платформой перенос ресурсов IaaS (виртуальных машин, виртуальных сетей и учетных записей хранения) из классической модели в модель Azure Resource Manager (ARM) с помощью команд Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: a57337acadafe40839e16d6a31861ff7c892c071
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602507"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell
Ниже последовательно описано, как использовать команды Azure PowerShell для переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager.

Если требуется, можно также перенести ресурсы с помощью [интерфейса командной строки Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Общие сведения о поддерживаемых сценариях переноса см. в разделе [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Подробное руководство и инструкции по переносу см. в разделе [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md)

<br>
Ниже приведена блок-схема с последовательностью действий во время переноса.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Шаг 1. Планирование миграции
Ниже приведено несколько рекомендаций для оценки переноса ресурсов IaaS из классической модели в модель Resource Manager.

* Ознакомьтесь с [поддерживаемыми и неподдерживаемыми компонентами и конфигурациями](migration-classic-resource-manager-overview.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или компоненты, мы рекомендуем отложить перенос до того момента, пока не будет заявлено об их поддержке. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
* Если у вас есть текущие автоматизированные сценарии, которые развертывают инфраструктуру и приложения, попробуйте создать аналогичную программу установки для миграции с помощью этих сценариев. Вы можете также настроить примеры среды с помощью портала Azure.

> [!IMPORTANT]
> В настоящее время не поддерживается перенос шлюзов приложений из классической модели в модель Resource Manager. Чтобы перенести классическую виртуальную сеть со шлюзом приложений, удалите этот шлюз перед выполнением операции подготовки для перемещения сети. После завершения переноса повторно подключите шлюз в Azure Resource Manager.
>
>Шлюзы ExpressRoute, подключенные к каналам ExpressRoute в другой подписке, перенести автоматически невозможно. В таких случаях удалите шлюз ExpressRoute, перенесите виртуальную сеть и создайте шлюз заново. Дополнительные сведения см. в статье [Перенос каналов ExpressRoute и связанных виртуальных сетей из классической модели развертывания на модель Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Шаг 2. Установка последней версии Azure PowerShell
Есть два основных способа установки Azure PowerShell — с помощью [коллекции PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) и [установщика веб-платформы (WebPI)](http://aka.ms/webpi-azps). Обновления для установщика веб-платформы выпускаются ежемесячно. Обновления для коллекции PowerShell выпускаются на постоянной основе. В этой статье используется Azure PowerShell 2.1.0.

Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Шаг 3. Проверка наличия у вас прав администратора подписки на портале Azure
Чтобы выполнить миграцию, вас нужно добавить как соадминистратора подписки на [портале Azure](https://portal.azure.com).

1. Войдите на [портал Azure](https://portal.azure.com).
2. В главном меню выберите **Подписка**. Если вы не видите этот пункт, щелкните **Все службы**.
3. Найдите нужную запись подписки, а затем посмотрите на поле **Моя роль**. Для соадминистратора значение должно быть _Администратор учетной записи_.

Если вам не удалась добавить соадминистратора, обратитесь к администратору или соадминистратору служб для подписки, чтобы вас добавили.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Шаг 4. Настройка подписки и регистрация для миграции
Сначала запустите командную строку PowerShell. Для переноса необходимо настроить среду как для классической модели, так и для модели Resource Manager.

Войдите в учетную запись для модели Resource Manager.

```powershell
    Connect-AzureRmAccount
```

Получите доступные подписки с помощью следующей команды.

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Задайте подписку Azure для текущего сеанса. В этом примере задается имя подписки по умолчанию **My Azure Subscription**. Замените имя подписки в примере своим собственным значением.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Регистрация — однократное действие, но, прежде чем выполнять миграцию, вам нужно зарегистрироваться. Если вы не зарегистрируетесь, отобразится такое сообщение об ошибке:
>
> *Неправильный запрос: Подписка не зарегистрирована для миграции.*
>
>

Выполните регистрацию в поставщике ресурсов миграции с помощью приведенной ниже команды.

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Подождите пять минут для завершения регистрации. Состояние утверждения регистрации можно проверить, выполнив следующую команду.

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Убедитесь, что RegistrationState имеет значение `Registered` , прежде чем продолжить.

Теперь войдите в учетную запись для классической модели.

```powershell
    Add-AzureAccount
```

Получите доступные подписки с помощью следующей команды.

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Задайте подписку Azure для текущего сеанса. В этом примере задается имя подписки по умолчанию **My Azure Subscription**. Замените имя подписки в примере своим собственным значением.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Шаг 5. Проверка наличия достаточного числа виртуальных ЦП для виртуальных машин Azure, развертываемых с помощью Resource Manager, в регионе Azure, в котором находится текущее развертывание или виртуальная сеть
Чтобы проверить текущее количество виртуальных ЦП в Azure Resource Manager, используйте приведенную ниже команду PowerShell. Чтобы узнать больше о квотах на виртуальные ЦП, см. соответствующий раздел статьи [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

В этом примере проверяется доступность в регионе **Западная часть США**. Замените регион в примере своим собственным значением.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Шаг 6. Выполнение команд для переноса ресурсов IaaS
> [!NOTE]
> Все операции, описанные здесь, являются идемпотентными. Если вы столкнетесь с какой-либо проблемой, не связанной с неподдерживаемой функцией или ошибкой конфигурации, мы рекомендуем повторить подготовку, прервать или зафиксировать текущую операцию. Платформа попытается повторить это действие.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Шаг. 6.1. Вариант 1. Миграция виртуальных машин в облачную службу (не в виртуальную сеть)
Получите список облачных служб, выполнив следующую команду, а затем выберите облачную службу для переноса. Если виртуальные машины в облачной службе размещены в виртуальной сети или им назначены веб-роли или рабочие роли, то команда возвращает сообщение об ошибке.

```powershell
    Get-AzureService | ft Servicename
```

Получите имя развертывания для облачной службы. В этом примере имя службы — **My Service**. Замените имя службы в примере своим собственным значением.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Подготовьте к переносу виртуальные машины в облачной службе. Возможно два варианта.

* **Вариант 1. Миграция виртуальных машин в виртуальную сеть, созданную платформой**

    Во-первых, проверьте возможность переноса облачной службы с помощью следующей команды.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к этапу **подготовки** ниже.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Вариант 2. Миграция виртуальных машин в существующую виртуальную сеть в модели развертывания с помощью Resource Manager**

    В этом примере группе ресурсов присваивается имя **myResourceGroup**, виртуальной сети — имя **myVirtualNetwork**, а подсети — имя **mySubNet**. Замените имена в примере именами своих ресурсов.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Во-первых, проверьте возможность переноса виртуальной сети с помощью следующей команды.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

После успешного завершения операции подготовки для одного из перечисленных выше вариантов запросите состояние миграции виртуальных машин. Убедитесь, что они находятся в состоянии `Prepared` .

В этом примере виртуальной машине присваивается имя **myVM**. Замените имя в примере именем своей виртуальной машины.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Проверьте конфигурацию для подготовленных ресурсов с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, выполните следующую команду.

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Шаг 6.1. Вариант 2. Миграция виртуальных машин в виртуальной сети

Для миграции виртуальных машин в виртуальной сети переносится сама виртуальная сеть. Виртуальные машины автоматически переносятся вместе с ней. Выберите виртуальную сеть, в которую будете переносить ресурсы.
> [!NOTE]
> [Перенесите отдельную классическую виртуальную машину](migrate-single-classic-to-resource-manager.md), создав виртуальную машину Resource Manager с управляемыми дисками на основе VHD-файлов (диска ОС и дисков данных) исходной виртуальной машины.
<br>

> [!NOTE]
> Имя виртуальной сети может отличаться от приведенного на новом портале. На новом портале Azure отображается имя в формате `[vnet-name]`, но фактическое имя виртуальной сети имеет тип `Group [resource-group-name] [vnet-name]`. Перед выполнением миграции найдите фактическое имя виртуальной сети с помощью команды `Get-AzureVnetSite | Select -Property Name` или просмотрите его на старом портале Azure. 

В этом примере виртуальной сети присваивается имя **myVnet**. Замените имя виртуальной сети в примере своим собственным значением.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Если в виртуальной сети есть виртуальные машины, веб-роли или рабочие роли с неподдерживаемыми конфигурациями, то отображается сообщение об ошибке проверки.
>
>

Во-первых, проверьте возможность переноса виртуальной сети с помощью следующей команды.

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Проверьте конфигурацию для подготовленных виртуальных машин с помощью Azure PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, выполните следующую команду.

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>Шаг 6.2. Перенос учетной записи хранения
После миграции виртуальных машин рекомендуется перенести учетную запись хранения.

Прежде чем перенести учетную запись хранения, выполните проверку выполнения предварительных требований.

* **Перенесите классические виртуальные машины, диски которых хранятся в учетной записи хранения.**

    Приведенная выше команда возвращает свойства RoleName и DiskName всех дисков классических виртуальных машин в учетной записи хранения. RoleName — это имя виртуальной машины, к которой подключен диск. Если приведенная выше команда вернула диски, убедитесь, что виртуальные машины, к которым подключены эти диски, будут перенесены до переноса учетной записи хранения.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Удалите неподключенные диски классических виртуальных машин, хранящиеся в учетной записи хранения.**

    Чтобы найти неподключенные диски классических виртуальных машин в учетной записи хранения, выполните следующую команду.

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Если приведенная выше команда вернула диски, удалите их, выполнив следующую команду.

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Удалите образы виртуальных машин, хранящиеся в учетной записи хранения.**

    Приведенная выше команда возвращает все образы виртуальных машин, диск ОС которых хранится в учетной записи хранения.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     Приведенная выше команда возвращает все образы виртуальных машин, диски данных которых хранятся в учетной записи хранения.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Удалите все образы виртуальных машин, возвращенные приведенными выше командами, с помощью следующей команды.
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Подготовьте каждую учетную запись хранения к переносу, используя следующую команду. В этом примере имя учетной записи хранения — **myStorageAccount**. Замените имя в примере именем своей учетной записи хранения.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

Далее необходимо подготовить учетную запись хранения к миграции.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Проверьте конфигурацию для подготовленной учетной записи хранения с помощью Azure PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, выполните следующую команду.

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Дополнительная информация
* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Планирование переноса ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
