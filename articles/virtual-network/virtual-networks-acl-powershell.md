---
title: Управление списками управления доступом конечной точки Azure | PowerShell | Классическая модель | Документация Майкрософт
description: Узнайте, как управлять списками ACL с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23124892"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Управление списками управления доступом конечной точки с помощью PowerShell в классической модели развертывания
Вы можете создавать сетевые списки управления доступом (ACL) для конечных точек, а также управлять ими с помощью модуля Azure PowerShell или портала управления. В этом разделе мы расскажем о том, как выполнять наиболее распространенные действия со списками ACL с помощью PowerShell. Список командлетов Azure PowerShell см. в [этой статье](http://go.microsoft.com/fwlink/?LinkId=317721). Дополнительные сведения о списках ACL см. в статье [Список управления доступом (ACL) конечной точки](virtual-networks-acl.md). Сведения об управлении списками ACL с помощью портала управления см. в статье [Настройка конечных точек в классической виртуальной машине Windows в Azure](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Управление сетевыми списками ACL с помощью Azure PowerShell
Сетевые списки ACL можно создавать, удалять и настраивать с помощью командлетов Azure PowerShell. Ниже приведены несколько примеров такой настройки.

Чтобы получить полный список командлетов ACL PowerShell, воспользуйтесь одной из таких команд:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Создание сетевого списка ACL с правилами, которые разрешают доступ из удаленной подсети
В следующем примере приведен способ создания нового списка ACL с правилами. Затем этот список применяется к конечной точке виртуальной машины. Правила списка ACL в примере ниже разрешают доступ из удаленной подсети. Чтобы создать сетевой список ACL с разрешающими правилами для удаленной подсети, откройте интегрированную среду сценариев Azure PowerShell. Скопируйте и вставьте приведенный ниже сценарий, задайте собственные значения и выполните его.

1. Создайте объект сетевого списка ACL.
   
        $acl1 = New-AzureAclConfig
2. Настройте правило, разрешающее доступ из удаленной подсети. В нашем примере настраивается правило *100*, которое имеет приоритет над другими правилами, начиная с 200. Оно разрешает доступ к конечной точке виртуальной машины из удаленной подсети *10.0.0.0/8*. Замените значения с учетом собственных требований к конфигурации, а вместо имени SharePoint ACL config укажите собственное, понятное вам, имя правила.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Чтобы настроить дополнительные правила, используйте тот же командлет и заменяйте значения с учетом собственных требований к конфигурации. Не забудьте изменить порядок номеров правил в соответствии с порядком, в котором они должны применяться. Чем меньше номер правила, тем больший у него приоритет.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Теперь вы можете создать (Add) новую конечную точку или задать (Set) список ACL для существующей конечной точки. В этом примере мы добавим новую конечную точку виртуальной машины с именем web и добавим в нее настройки ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Далее нужно объединить командлеты и выполнить сценарий. В нашем примере объединенные командлеты будут выглядеть так:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Удаление из сетевого списка ACL правила, разрешающего доступ из удаленной подсети
В следующем примере приведен способ удаления правила из сетевого списка ACL.  Чтобы удалить из списка правило, разрешающее доступ из удаленной подсети, откройте интегрированную среду сценариев Azure PowerShell. Скопируйте и вставьте приведенный ниже сценарий, задайте собственные значения и выполните его.

1. Первым делом нужно получить объект сетевого списка ACL для конечной точки виртуальной машины, после чего из него можно будет удалить правило. В этом случае мы удаляем правило по его идентификатору. Из списка ACL удаляется только правило с идентификатором 0. Сам объект списка ACL не удаляется с конечной точки виртуальной машины.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Далее вам нужно применить объект сетевого списка ACL к конечной точке виртуальной машины и обновить виртуальную машину.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Удаление сетевого списка ACL с конечной точки виртуальной машины
Иногда объект сетевого списка ACL нужно удалить с конечной точки виртуальной машины. Для этого откройте интегрированную среду сценариев Azure Powershell. Скопируйте и вставьте приведенный ниже сценарий, задайте собственные значения и выполните его.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Дополнительная информация
[Сетевой список управления доступом](virtual-networks-acl.md)

