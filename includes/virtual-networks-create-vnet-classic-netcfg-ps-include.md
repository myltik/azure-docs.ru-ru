---
title: включение файла
description: включение файла
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 4ae4c3100ae13fdb05e17974b433b247128c1a50
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805079"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Как создать виртуальную сеть с помощью файла конфигурации сети в PowerShell
В Azure для определения всех виртуальных сетей, доступных для подписки, используется XML-файл. Вы можете скачать этот файл и внести в него изменения, чтобы изменить или удалить существующие виртуальные сети и создать новые. Из этого руководства вы узнаете, как скачать этот файл, который называется файлом конфигурации сети (или NETCGF), и внести в него изменения для создания виртуальной сети. Дополнительные сведения о файле конфигурации сети см. в статье [Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Схема конфигурации виртуальной сети Azure).

Чтобы создать виртуальную сеть в PowerShell, используя файл NETCGF, сделайте следующее:

1. Если вы ранее не использовали Azure PowerShell, выполните действия по [установке и настройке Azure PowerShell](/powershell/azureps-cmdlets-docs). Затем войдите в учетную запись Azure и выберите подписку.
2. В консоли Azure PowerShell воспользуйтесь командлетом **Get-AzureVnetConfig**, чтобы скачать файл конфигурации сети в каталог на компьютере, выполнив указанную ниже команду. 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Ожидаемые выходные данные:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Откройте файл, сохраненный при выполнении шага 2, в любом XML- или текстовом редакторе и найдите элемент **<VirtualNetworkSites>**. Если вы уже создавали сети, для каждой сети будет указан ее собственный элемент **<VirtualNetworkSite>**.
4. Для создания виртуальной сети, описанной в этом сценарии, добавьте следующий XML-код сразу после элемента **<VirtualNetworkSites>** :

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Сохраните файл конфигурации сети.
6. В консоли Azure PowerShell воспользуйтесь командлетом **Set-AzureVnetConfig**, чтобы отправить файл конфигурации сети, выполнив следующую команду: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Возвращаемые выходные данные:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Если в возвращаемых выходных данных значение параметра **OperationStatus** отлично от *Succeeded*, проверьте XML-файл на наличие ошибок и повторите шаг 6.

7. В консоли Azure PowerShell воспользуйтесь командлетом **Get AzureVnetSite**, чтобы убедиться в том, что новая сеть добавлена, выполнив следующую команду: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Возвращаемые выходные данные (сокращенные) содержат следующий текст:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
