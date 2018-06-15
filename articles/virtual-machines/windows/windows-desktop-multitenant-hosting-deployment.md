---
title: Как развернуть Windows 10 в Azure с правами на мультитенантное размещение
description: Узнайте, как воспользоваться преимуществами программы Software Assurance для Windows, чтобы перенести свои локальные лицензии в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 5952c602a90568a9ce9e71dfa2c0dd383aed4e16
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915377"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Как развернуть Windows 10 в Azure с правами на мультитенантное размещение 
Клиентам, использующим Windows 10 Корпоративная E3 или Windows 10 Корпоративная E5 для каждого пользователя, либо Windows VDA для каждого пользователя (лицензии на подписку пользователя или дополнительные лицензии на подписку пользователя), права на мультитенантное размещение для Windows 10 позволяют перенести лицензии Windows 10 в облако и запустить виртуальные машины Windows 10 в Azure без необходимости платить за другую лицензию. Дополнительные сведения см. в [этом разделе](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> В этой статье показано, как реализовать преимущество лицензирования для образов рабочего стола Windows 10 Pro в Azure Marketplace.
> - При использовании образов Windows 7, 8.1 и 10 Корпоративная (x64) в Azure Marketplace для подписок MSDN см. статью [Использование клиента Windows в Azure для сценариев разработки и тестирования](client-images.md).
> - Чтобы воспользоваться преимуществами лицензирования Windows Server, см. статью [Преимущество гибридного использования Azure для Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Развертывание образа Windows 10 из Azure Marketplace 
Для развертывания с помощью PowerShell, CLI или шаблонов Azure Resource Manager образ Windows 10 можно найти со следующими именем издателя, предложением и номером SKU.

| ОС  |      PublisherName      |  ПРЕДЛОЖЕНИЕ | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Профессиональная    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Профессиональная    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Загрузка виртуального жесткого диска Windows 10 в Azure
Если вы отправляете обобщенный виртуальный жесткий диск (VHD) Windows 10, учтите, что в Windows 10 встроенная учетная запись администратора не включена по умолчанию. Чтобы включить ее, добавьте следующую команду в качестве элемента расширения пользовательского скрипта.

```powershell
Net user <username> /active:yes
```

Следующий фрагмент PowerShell предназначен для того, чтобы пометить все учетные записи администратора как активные, включая встроенную учетную запись администратора. Вам подойдет этот пример, если имя пользователя встроенной учетной записи администратора неизвестно.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Дополнительные сведения 
* [Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure](upload-generalized-managed.md)
* [Подготовка диска VHD или VHDX для Windows к отправке в Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Развертывание Windows 10 с правами на мультитенантное размещение
Убедитесь, что у вас [установлена и настроена последняя версия Azure PowerShell](/powershell/azure/overview). Чтобы передать подготовленный виртуальный жесткий диск в учетную запись хранения Azure, выполните командлет `Add-AzureRmVhd` со следующими параметрами:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Развертывание с помощью шаблона Azure Resource Manager** В шаблонах Resource Manager можно указать дополнительный параметр для `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../../resource-group-authoring-templates.md). После загрузки виртуального жесткого диска в Azure необходимо изменить шаблон Resource Manager, чтобы включить в него тип лицензирования как часть поставщика вычислительных ресурсов и развернуть шаблон в обычном режиме.
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Развертывание через PowerShell** При развертывании виртуальной машины Windows Server с помощью PowerShell доступен дополнительный параметр для `-LicenseType`. После передачи виртуального жесткого диска в Azure необходимо создать новую виртуальную машину с помощью командлета `New-AzureRmVM` и указать тип лицензирования следующим образом.
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Проверка наличия льготы на гибридное использование Azure для Windows Server
После развертывания виртуальной машины с помощью Resource Manager или PowerShell проверьте тип лицензии с помощью командлета `Get-AzureRmVM` следующим образом.
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Выходные данные аналогичны приведенному ниже примеру для Windows 10 с правильным типом лицензии:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Эти выходные данные отличаются от полученных при развертывании виртуальной машины без преимуществ гибридного использования Azure (например, при развертывании виртуальной машины непосредственно из коллекции Azure).

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Дополнительная информация о присоединении к Azure AD
>[!NOTE]
>Azure подготавливает все виртуальные машины Windows со встроенной учетной записью администратора, которую нельзя использовать для присоединения к AAD. Например, выбор команды *Параметры > Учетная запись > Доступ к рабочей или учебной учетной записи > + Подключиться* не сработает. Чтобы присоединиться к Azure AD вручную, необходимо создать журнал и войти в систему, используя учетную запись второго администратора. Можно также настроить Azure AD, используя пакет подготовки. Дополнительные сведения можно получить по ссылке в разделе *Дальнейшие действия*.
>
>

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [настройке VDA для Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation).
- Узнайте больше о [правах на мультитенантное размещение для Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).


