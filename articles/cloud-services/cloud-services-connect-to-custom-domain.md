<properties
  pageTitle="Подключение ролей облачных служб Azure к контроллеру личного домена AD, размещенному в Azure"
  description="Сведения о подключении веб-ролей и рабочих ролей к личному домену AD с помощью Powershell и расширения домена AD"
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/05/2015"
    ms.author="vmaker"/>

# Подключение ролей облачных служб Azure к контроллеру личного домена AD, размещенному в Azure

## Пошаговое руководство по подключению веб-ролей или рабочих ролей Azure к контроллеру личного домена, размещенному на Azure

Сначала настройте виртуальную сеть (VNET) в Azure. Затем добавьте контроллер домена Active Directory (размещенный на виртуальной машине Azure) к виртуальной сети. Следующим шагом добавьте существующие облачные роли служб в заранее созданную виртуальную сеть и последовательно подключите их к контроллеру домена.

Прежде чем начать, пара моментов, которые стоит запомнить: 1. В этом учебнике используется Powershell, поэтому убедитесь, что Azure Powershell установлен и готов к использованию. Справку об установке Azure Powershell см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md). 2. Экземпляры контроллера домена AD и веб-ролей или рабочих ролей должны быть в виртуальной сети.

Следуйте этому пошаговому руководству и, если возникнут проблемы, оставьте нам комментарий ниже. Кто-нибудь из наших сотрудников вам ответит (да, мы читаем ваши комментарии).

## Создайте виртуальную сеть

Создать виртуальную сеть в Azure можно с помощью портала Azure или Powershell. В этом учебнике используется Powershell. Информацию о создании виртуальной сети с помощью портала Azure см. в разделе [Создание виртуальной сети](../create-virtual-network.md).

    #Create Virtual Network

    $vnetStr =
    @"<?xml version="1.0" encoding="utf-8"?>
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
            <AddressSpace>
              <AddressPrefix>[your-address-prefix]</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="[your-subnet-name]">
                <AddressPrefix>[your-subnet-range]</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    "@;

    $vnetConfigPath = "<path-to-vnet-config>"
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## Создание виртуальной машины

Выполнив настройку виртуальной сети, необходимо будет создать контроллер домена AD. В этом учебнике выполняется настройка контроллера домена AD на виртуальной машине Azure.

Для этого создайте виртуальную машину с помощью Powershell, используя приведенную ниже команду.

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = ‘<your-hosted-service>’
    $vm1 = ‘<your-vm-name>’
    $username = ‘<your-username>’
    $password = ‘<your-password>’
    $ affgrp = ‘<your- affgrp>’

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## Повысьте уровень виртуальной машины до контроллера домена
Чтобы настроить виртуальную машину в качестве контроллера домена AD, необходимо войти в нее и настроить.

Чтобы войти в виртуальную машину, можно получить файл RDP через Powershell, используя приведенную ниже команду.

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

Войдя в виртуальную машину, настройте ее в качестве контроллера домена AD, следуя указаниям пошагового руководства [Настройка личного контроллера домена AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## Добавьте развертывание облачной службы в виртуальную сеть

Затем необходимо добавить развертывание облачной службы в созданную виртуальную сеть. Для этого измените файл CSCFG облачной службы, добавив в него соответствующие разделы с помощью Visual Studio или другого редактора.

    <ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
        <Role name="[role-name]">
        <Instances count="[number-of-instances]" />
      </Role>
      <NetworkConfiguration>

        <!--optional-->
        <Dns>
          <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
        </Dns>
        <!--optional-->

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

Затем создайте проект облачной службы и выполните его развертывание в Azure. Для получения справки о развертывании пакета облачных служб в Azure см. раздел [Создание и развертывание облачной службы](cloud-services-how-to-create-deploy.md#deploy).

## Подключите веб-роли и рабочие роли к личному домену с помощью расширения доменов AD

Выполнив развертывание проекта облачной службы в Azure, подключите экземпляры ролей к личному домену AD с помощью расширения доменов AD. Чтобы добавить расширение доменов AD к существующему развертыванию облачной службы и присоединить личный домен, выполните в Powershell следующие команды:

    #Initialize domain variables

    $domain = ‘<your-domain-name>’;
    $dmuser = ‘$domain<your-username>’;
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

Вот и все.

Облачные службы должны быть присоединены к контроллеру личного домена. Дополнительные сведения о различных параметрах настройки расширения домена AD см. в справке PowerShell, как показано ниже.

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

Так же хотелось бы получить от вас отзыв по поводу того, будет ли полезным расширение, которое повышает уровень виртуальной машины до контроллера домена. Если думаете, что будет, дайте нам знать в разделе комментариев.

Надеемся, эта информация была вам полезна!
 

<!---HONumber=July15_HO4-->