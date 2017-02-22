---
title: "Доменные службы Azure Active Directory: руководство по администрированию | Документация Майкрософт"
description: "Присоедините виртуальную машину Windows к управляемому домену, используя Azure PowerShell и классическую модель развертывания."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: fdc53ee24c623218e218cdda1c24dffbbec50bc5
ms.openlocfilehash: e7c7e9504985fa98185286ee9a28cec80fb87df6


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Присоединение виртуальной машины Windows Server к управляемому домену с помощью PowerShell
> [!div class="op_single_selector"]
> * [Классический портал Azure — Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell — Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
> 
> 

<br>

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Сейчас доменные службы Azure AD не поддерживают модель Resource Manager.
> 
> 

Ниже показано, как настроить набор команд Azure PowerShell для создания и предварительной настройки виртуальной машины Azure под управлением Windows, используя подход на базе стандартных блоков. Приведенные ниже шаги помогут создать виртуальную машину Azure под управлением Windows и присоединить ее к управляемому домену доменных служб Azure AD.

Материал в этих шагах для создания наборов команд Azure PowerShell представлен таким образом, что достаточно лишь заполнить пробелы. Это удобно, если вы не работали с PowerShell или хотите знать, какие именно значения отвечают за работоспособность конфигурации. Опытные пользователи PowerShell могут подставить в команды собственные значения для переменных (строки, начинающиеся со знака "$").

Если вы еще не сделали этого, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](/powershell/azureps-cmdlets-docs) , чтобы установить Azure PowerShell на локальном компьютере. Затем откройте командную строку Windows PowerShell.

## <a name="step-1-add-your-account"></a>Шаг 1. Добавление учетной записи
1. В командной строке PowerShell введите **Add-AzureAccount** и нажмите клавишу **ВВОД**.
2. Введите адрес электронной почты, связанный с подпиской Azure, и нажмите кнопку **Продолжить**.
3. Введите пароль к учетной записи.
4. Щелкните **Войти**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Шаг 2. Выбор подписки и учетной записи хранения
Укажите подписку Azure и учетную запись хранения, выполнив следующие команды в командной строке Windows PowerShell. Замените все содержимое внутри кавычек, включая знаки < и >, правильными именами.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Правильное имя подписки можно получить из свойства SubscriptionName в выходных данных команды **Get-AzureSubscription** . Правильное имя учетной записи хранения можно получить из свойства Label в выходных данных команды **Get-AzureStorageAccount** после выполнения команды **Select-AzureSubscription**.

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Шаг 3. Пошаговое руководство — подготовка виртуальной машины и ее присоединение к управляемому домену
Вот соответствующий набор команд Azure PowerShell для создания такой виртуальной машины, в котором для удобства чтения между блоками вставлены пустые строки.

Укажите сведения о подготавливаемой виртуальной машине Windows.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Сведения о значениях InstanceSize для виртуальных машин серии D, DS или G см. в разделе [Размеры виртуальных машин и облачных служб для Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Предоставьте сведения об управляемом домене.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Укажите имя облачной службы.

    $svcname="Contoso100-test"

Укажите имя виртуальной сети, к которой следует присоединить эту виртуальную машину. Убедитесь, что управляемый домен доменных служб AAD доступен в этой виртуальной сети.

    $vnetname="MyPreviewVnet"

Выберите образ виртуальной машины, который будет использоваться для подготовки виртуальной машины.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Настройте виртуальную машину — укажите ее имя, размер экземпляра и образ, который должен использоваться.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Получите учетные данные локального администратора для этой виртуальной машины. Укажите надежный пароль локального администратора.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Получите учетные данные для учетной записи пользователя, относящейся к группе "Администраторы DC AAD", чтобы присоединить виртуальную машину к управляемому домену. Не указывайте имя домена — например, в нашем примере в качестве имени пользователя мы указываем "bob".

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Настройте виртуальную машину — укажите требование присоединения к домену и необходимые учетные данные.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Задайте подсеть для виртуальной машины.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Необязательно: укажите IP-адрес домена. Если в качестве IP-адресов управляемого домена доменных служб Azure AD используются DNS-серверы для виртуальной сети, этот шаг не требуется.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Теперь подготовьте присоединенную к домену виртуальную машину Windows.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Сценарий для подготовки виртуальной машины Windows и ее автоматического присоединения к управляемому домену доменных служб AAD
Этот набор команд PowerShell создает виртуальную машину для бизнес-сервера со следующими свойствами:

* использует образ Windows Server 2012 R2 Datacenter;
* имеет размер "очень малая";
* имеет имя Contoso100-test;
* является автоматически присоединяемой к управляемому домену contoso100;
* добавляется в ту же виртуальную сеть, что и управляемый домен.

Ниже приведен полный пример сценария для создания виртуальной машины Windows и ее автоматического присоединения к управляемому домену доменных служб Azure AD.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Похожий контент
* [Приступая к работе с доменными службами Azure AD](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Администрирование управляемого домена доменных служб Azure AD)](active-directory-ds-admin-guide-administer-domain.md)




<!--HONumber=Jan17_HO1-->


