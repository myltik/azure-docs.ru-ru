---
title: Обновление 1804 для Azure Stack | Документация Майкрософт
description: Узнайте, что находится в обновлении 1804 для интегрированных систем Azure Stack, о каких проблемах известно и где можно скачать обновление.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a7ba5f1947da09177e7d2d9d0e9e926d858dff7e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302596"
---
# <a name="azure-stack-1804-update"></a>Обновление 1804 для Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье представлены улучшения и исправления в пакете обновления 1804, известные проблемы выпуска и сведения о том, где можно скачать обновление. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу обновления и проблемы со сборкой (после установки).

> [!IMPORTANT]        
> Этот пакет обновления предназначен только для интегрированных систем Azure Stack. Не применяйте этот пакет обновления к Пакету средств разработки Azure Stack.

## <a name="build-reference"></a>Указание сборки    
Номер сборки обновления 1804 для Azure Stack — **20180513.1**.   

### <a name="new-features"></a>новые функции;
Это обновление включает в себя следующие улучшения и исправления для Azure Stack.

- <!-- 15028744 - IS -->  **Visual Studio support for disconnected Azure Stack deployments using AD FS**. Within Visual Studio you now can add subscriptions and authenticate using AD FS federated User credentials. 
 
- <!-- 1779474, 1779458 - IS --> **Use Av2 and F series virtual machines**. Azure Stack can now use virtual machines based on the Av2-series and F-series virtual machine sizes. For more information see [Virtual machine sizes supported in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available after the update installs:
  - *Подписка поставщика по умолчанию*. Используйте эту подписку только для базовой инфраструктуры. В ней не следует развертывать ресурсы или поставщики ресурсов.
  - *Подписка для контроля использования*. Используйте эту подписку для развертывания поставщика ресурсов. За ресурсы, развертываемые в этой подписке, плата не взимается.
  - *Подписка для контроля потребления*. Используйте эту подписку для прочих рабочих нагрузок, которые требуется развернуть. За ресурсы, развертываемые в ней, взимается плата в соответствии с обычными ценами.


## <a name="fixed-issues"></a>Исправленные проблемы

- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information.
 
- <!-- 2050709  -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.
 
- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option.

- **Различные исправления** производительности, стабильности, безопасности и операционной системы, используемой службой Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Дополнительные выпуски, приуроченные к этому обновлению  
Ниже приведены компоненты, которые теперь доступны, но не требуют установки обновления 1804 для Azure Stack.
- **Обновление для пакета мониторинга System Center Operations Manager для Microsoft Azure Stack**. Новая версия (1.0.3.0) пакета мониторинга System Center Operations Manager для Microsoft Azure Stack доступна для [скачивания](https://www.microsoft.com/download/details.aspx?id=55184). С этой версией можно использовать субъекты-службы при добавлении подключенного к сети развертывания Azure Stack. Эта версия также включает в себя функции управления обновлениями, которые дают возможность выполнять действия по исправлению непосредственно из Operations Manager. В нее добавлены новые панели мониторинга, отображающие поставщики ресурсов, единицы масштабирования и узлы единиц масштабирования.

- **Новая версия модуля PowerShell для ресурсов администратора Azure Stack (1.3.0)**.  Модуль PowerShell для Azure Stack версии 1.3.0 теперь доступен для установки. Эта версия включает в себя команды для всех поставщиков ресурсов администратора, предназначенных для управления Azure Stack.  В этом выпуске будет считаться устаревшим некоторое содержимое [репозитория](https://github.com/Azure/AzureStack-Tools) инструментов GitHub для Azure Stack. 

   Сведения об установке приведены в [указаниях](azure-stack-powershell-install.md) или [справке](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) для модуля Azure Stack 1.3.0. 

- **Первоначальный выпуск справочника по REST API Azure Stack**. Опубликован [справочник по API для всех поставщиков ресурсов администратора Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/). 


## <a name="before-you-begin"></a>Перед началом работы    

### <a name="prerequisites"></a>предварительным требованиям
- Прежде чем применить обновление 1804 для Azure Stack, установите [обновление 1803](azure-stack-update-1803.md) для Azure Stack.    

### <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления   
- Во время установки обновления 1804 могут отображаться оповещения с заголовком *Error – Template for FaultType UserAccounts.New is missing* (Ошибка: отсутствует шаблон для FaultType UserAccounts.New).  Эти оповещения можно игнорировать. Они будут закрыты автоматически после завершения обновления до версии 1804.   
 
- <!-- TBD - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).
### <a name="post-update-steps"></a>Действия после обновления
*Действия после обновления до версии 1804 отсутствуют.*



### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)
Ниже перечислены известные проблемы после установки сборки **20180513.1**.

#### <a name="portal"></a>Microsoft Azure
- <!-- TBD -  IS ASDK -->The ability [to open a new support request from the dropdown](azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Для интегрированных систем Azure Stack используйте https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Элемент навигации](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

- <!-- TBD - IS ASDK --> In the admin portal, you might see a critical alert for the *Microsoft.Update.Admin* component. The Alert name, description, and remediation all display as:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (Ошибка. Отсутствует шаблон FaultType ResourceProviderTimeout).

  Это оповещение можно проигнорировать. 


#### <a name="compute"></a>Службы вычислений
- <!-- TBD - IS --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Чтобы обойти эту проблему, используйте один из следующих методов для развертывания виртуальной машины. В каждом методе необходимо указать размер виртуальной машины, который вы хотите использовать.

  - **Шаблон Azure Resource Manager:** при использовании шаблона задайте в нем значение *vmSize*, равное желаемому размеру виртуальной машины. Например, приведенное ниже значение используется для развертывания виртуальной машины размера *F32s_v2*.  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** можно использовать команду [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) и указать размер виртуальной машины в качестве параметра. Пример: `--size "Standard_F32s_v2"`.

  - **PowerShell:** в PowerShell можно использовать командлет [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) с параметром, который указывает размер виртуальной машины. Пример: `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Сеть
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Чтобы обойти эту проблему, используйте план надстройки для повышения квоты сети, если план уже связан с подпиской. Чтобы получить дополнительные сведения, узнайте, как [сделать доступным план надстройки](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group. 

    Обходной путь для службы приложений. Если вам нужно подключение с помощью удаленного рабочего стола к экземплярам контроллера, измените правила безопасности в группах безопасности сети с помощью PowerShell.  Ниже приведены примеры того, как *разрешить* конфигурацию, а затем восстановить для нее состояние *запрета*:  
    
    - *Разрешить:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Запретить:*

        ```powershell
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```

#### <a name="sql-and-mysql"></a>SQL и MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1804, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially.  For example, if you use version 1802, first apply version 1803, and then update to 1804.      
>   
> Установка обновления 1804 не влияет на текущее использование поставщиков ресурсов SQL или MySQL пользователями.
> Независимо от версии используемых поставщиков ресурсов, данные пользователей в базах данных не затрагиваются и остаются доступными.    



#### <a name="app-service"></a>Служба приложений Azure
- <!-- TBD - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.


#### <a name="usage"></a>Использование  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Health and monitoring --> 
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Скачивание обновления
Пакет обновления 1804 для Azure Stack можно скачать [здесь](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>См. также
- Сведения об использовании привилегированной конечной точки (PEP) для отслеживания и возобновления обновлений см. в статье [Мониторинг обновлений в Azure Stack с помощью привилегированной конечной точки](azure-stack-monitor-update.md).
- [Общие сведения об управлении обновлениями в Azure Stack](azure-stack-updates.md).
- [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).
