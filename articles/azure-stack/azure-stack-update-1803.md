---
title: Обновление 1803 Azure Stack | Документация Майкрософт
description: Узнайте, что находится в обновлении 1803 для интегрированных систем Azure Stack, об известных проблемах и где можно скачать обновление.
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
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 26c77b706f17f49eff782e6d0d73087050739874
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-1803-update"></a>Обновление 1803 Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье представлены улучшения и исправления в пакете обновлений 1803, известные проблемы выпуска и сведения о том, где можно скачать обновление. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу обновления и проблемы со сборкой (после установки).

> [!IMPORTANT]        
> Этот пакет обновления предназначен только для интегрированных систем Azure Stack. Не применяйте этот пакет обновления к Пакету средств разработки Azure Stack.

## <a name="build-reference"></a>Указание сборки    
Номер сборки обновления 1803 Azure Stack — **20180329.1**.


## <a name="before-you-begin"></a>Перед началом работы    
> [!IMPORTANT]    
> Не пытайтесь создавать виртуальные машины во время установки этого обновления. Дополнительные сведения об управлении обновлениями в Azure Stack см. в [этой статье](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>предварительным требованиям
- Прежде чем применить обновление 1803 Azure Stack, установите [обновление 1802](azure-stack-update-1802.md) Azure Stack.    


### <a name="post-update-steps"></a>Действия после обновления
- Когда обновление 1803 установится, установите все применимые исправления. Дополнительные сведения см. в статьях базы знаний по ссылке ниже, а также в статье о нашей [политике обслуживания](azure-stack-servicing-policy.md).

  - Ознакомьтесь со статьей базы знаний [KB 4103348 об аварийном завершении работы службы API сетевого контроллера при попытке установить обновление Azure Stack](https://support.microsoft.com/en-us/help/4103348).

- После установки этого обновления проверьте конфигурацию брандмауэра, чтобы убедиться, что [необходимые порты](azure-stack-integrate-endpoints.md) открыты. Например, это обновление содержит Azure Monitor, что обуславливает замену журналов аудита журналами действий. Ввиду этого изменения теперь порт 13012 используется и также должен быть открыт.  

### <a name="new-features"></a>новые функции; 
Это обновление включает следующие улучшения и исправления для Azure Stack.

- **Обновления секретов Azure Stack** — (учетные записи и сертификаты). Дополнительные сведения об управлении секретами см. в статье [Смена секретов в Azure Stack](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   Кроме того, в рамках этого изменения в разделе **Больше служб** параметр *Журналы аудита* теперь отображается как *Журналы действий*. Функциональные возможности теперь согласуются с порталом Azure. 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   Разреженные файлы — это эффективный формат файлов, который позволяет снизить использование дискового пространства и оптимизировать операции ввода-вывода.  Дополнительные сведения см. в статье о [разреженных файлах Fsutil](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) для Windows Server. 

### <a name="fixed-issues"></a>Исправленные проблемы

- <!-- 1739988 --> Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 1868283 --> Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324 --> Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- При создании виртуальной машины больше не отображается сообщение *Не удалось отобразить цены*, когда вы выбираете размер виртуальной машины.

- Различные исправления производительности, стабильности, безопасности и операционной системы, которую использует служба Azure Stack.


### <a name="changes"></a>изменения
- Изменился способ изменения состояния созданного предложения с *частного* на *общедоступное* или *резервное*. Дополнительные сведения см. в статье [Создание предложения в Azure Stack](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления    
<!-- 2328416 --> During installation of the 1803 update, there can be downtime of the blob service and internal services that use blob service. This includes some virtual machine operations. This down time can cause failures of tenant operations or alerts from services that can’t access data. This issue resolves itself when the update completes installation. 


### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)
Ниже перечислены известные проблемы после установки для сборки **20180323.2**.

#### <a name="portal"></a>Microsoft Azure
- Возможность [открыть новый запрос на поддержку из раскрывающегося списка](azure-stack-manage-portals.md#quick-access-to-help-and-support) на портале администрирования недоступна. Вместо этого перейдите по следующей ссылке:     
    - Для интегрированных систем Azure Stack используйте https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Просмотр вычислений или ресурсов хранилища на портале администрирования может оказаться невозможным. Причиной является ошибка, которая произошла во время установки обновления. Из-за нее об обновлении было неверно сообщено (как об успешном). При возникновении этой ошибки обратитесь за помощью в службу поддержки корпорации Майкрософт.

- На портале может появиться пустая панель мониторинга. Чтобы восстановить панель мониторинга, щелкните значок шестеренки в правом верхнем углу портала и выберите **Восстановление параметров по умолчанию**.

- При просмотре свойств ресурсов или группы ресурсов кнопка **Перемещение** отключена. Это ожидаемое поведение. Перемещение ресурсов или групп ресурсов между группами ресурсов или подписками в настоящее время не поддерживается.

- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.

- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. Используйте PowerShell, чтобы проверить разрешения.

- На панели мониторинга на портале администрирования плитка "Обновление" не отображает информацию об обновлениях. Для устранения этой проблемы щелкните плитку, чтобы обновить ее.

- На портале администрирования может появиться критическое оповещение о компоненте *Microsoft.Update.Admin*. В полях "Имя оповещения", "Описание" и "Исправление" отображается следующее сообщение:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (Ошибка. Отсутствует шаблон FaultType ResourceProviderTimeout).

  Это оповещение можно проигнорировать. 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Marketplace
- Пользователи могут просматривать весь Marketplace без подписки и видеть некоторые административные элементы, такие как планы и предложения. Эти элементы бесполезны для пользователей.



#### <a name="compute"></a>Среда выполнения приложений
- Параметры масштабирования для масштабируемых наборов виртуальной машины на портале недоступны. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Из-за различий между версиями PowerShell используйте параметр `-Name` вместо параметра `-VMScaleSetName`.

- При создании группы доступности на портале (**Создать** > **Вычисления** > **Группа доступности**) вы можете создать ее только с одним доменом сбоя и одним доменом обновления. В качестве обходного решения при создании виртуальной машины создайте группу доступности с помощью PowerShell, CLI или на портале.

- При создании виртуальных машин на пользовательском портале Azure Stack отображается неверное число дисков данных, которые можно подключить к виртуальной машине серии DS. К виртуальным машинам серии DS можно подключить такое же количество дисков данных, которое доступно в конфигурации Azure.

- При сбое создания образа виртуальной машины неисправный элемент, который вы не смогли удалить, может быть добавлен в колонку вычислений образов виртуальной машины.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

-  Если подготовка расширения для развертывания виртуальной машины занимает слишком много времени, следует дождаться истечения времени ожидания, а не пытаться остановить процесс освобождения или удаления виртуальной машины.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Сеть
- После создания виртуальной машины и ее связывания с общедоступным IP-адресом вы не можете отменить эту связь. Будет выглядеть так, будто вам удалось его отменить, но ранее присвоенный общедоступный IP-адрес останется связанным с исходной виртуальной машиной.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан.



- Azure Stack поддерживает один *локальный сетевой шлюз* на IP-адрес. Это относится ко всем подпискам клиентов. После создания первого подключения к локальному сетевому шлюзу последующие попытки создать ресурс локального сетевого шлюза с тем же IP-адресом блокируются.

- В виртуальной сети, созданной с параметром DNS-сервера *Автоматический*, попытка изменить на пользовательский DNS-сервер завершается ошибкой. Обновленные параметры не передаются на виртуальные машины в этой виртуальной сети.

- Azure Stack не поддерживает добавление дополнительных сетевых интерфейсов к экземпляру виртуальной машины после ее развертывания. Если для виртуальной машины требуется несколько сетевых интерфейсов, их нужно определить во время развертывания.

- <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Обходной путь для службы приложений. Если вам нужно подключение с помощью удаленного рабочего стола к экземплярам контроллера, измените правила безопасности в группах безопасности сети с помощью PowerShell.  Ниже приведены примеры того, как *разрешить* конфигурацию, а затем восстановить для нее состояние *запрета*:  
    
    - *Разрешить:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- Прежде чем продолжить, просмотрите важную заметку в разделе [Перед началом работы](#before-you-begin).

- После создания развертывания SQL или MySQL может пройти до одного часа, прежде чем пользователи смогут создавать в нем базы данных.

- Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается и может привести к несоответствию состояний.  


> [!NOTE]  
> После обновления до Azure Stack 1803 вы можете продолжить использование ранее развернутых поставщиков ресурсов SQL и MySQL.  Рекомендуем обновлять SQL и MySQL при выходе нового выпуска. Применяйте обновления для поставщиков ресурсов SQL и MySQL последовательно (как и для Azure Stack).  Например, если вы используете версию 1711, сначала примените версию 1712, затем — 1802, а затем обновите до версии 1803.      
>   
> Установка обновления 1803 не влияет на текущее использование поставщиков ресурсов SQL или MySQL пользователями.
> Независимо от версии используемых поставщиков ресурсов, данные пользователей в базах данных не затрагиваются и остаются доступными.    



#### <a name="app-service"></a>Служба приложений
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.

- Чтобы масштабировать инфраструктуру (рабочие роли, роли управления, внешние роли), используйте PowerShell, как описано в заметках о выпуске служб вычислений.


#### <a name="usage"></a>Использование  
- Вместо метки *TimeDate*, которая показывает время создания записи, в данных инструмента для отслеживания использования общедоступного IP-адреса отображается одно значение *EventDateTime* для каждой записи. В настоящее время эти данные непригодны для выполнения точного расчета и получения данных об использовании общедоступного IP-адреса.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Загрузка средств Azure Stack с сайта GitHub
- При использовании командлета PowerShell *invoke-webrequest* для загрузки средств Azure Stack с Github вы получите следующее сообщение об ошибке:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest. Запрос был прерван. Не удалось создать защищенный канал SSL/TLS).     

  Эта ошибка возникает из-за недавнего прекращения поддержки стандартов шифрования Tlsv1 и Tlsv1.1 в GitHub (по умолчанию для PowerShell). Дополнительные сведения см. в разделе [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Уведомление об удалении слабых стандартов шифрования).

  Чтобы устранить эту проблему, добавьте `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` в начало скрипта, чтобы консоль PowerShell использовала TLSv1.2 при загрузке из репозиториев GitHub.


## <a name="download-the-update"></a>Скачивание обновления
Пакет обновления 1803 Azure Stack можно скачать [здесь](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>См. также
- Сведения об использовании привилегированной конечной точки (PEP) для отслеживания и возобновления обновлений см. в статье [Мониторинг обновлений в Azure Stack с помощью привилегированной конечной точки](azure-stack-monitor-update.md).
- [Общие сведения об управлении обновлениями в Azure Stack](azure-stack-updates.md).
- [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).
