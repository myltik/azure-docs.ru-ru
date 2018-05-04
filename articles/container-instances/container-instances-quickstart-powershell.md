---
title: Краткое руководство. Создание первого контейнера службы "Экземпляры контейнеров Azure" с помощью PowerShell
description: В этом кратком руководстве вы развернете контейнер Windows в службе "Экземпляры контейнеров Azure" при помощи Azure PowerShell.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 8670de00e3ab66ec4a5fa5c0c340e38182fd1ffd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Краткое руководство. Создание первого контейнера в службе "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" упрощает создание контейнеров Docker и управление ими в Azure, избавляя от необходимости подготавливать виртуальные машины или применять службу более высокого уровня. В этом кратком руководстве вы создадите контейнер Windows в Azure и предоставите его в Интернете с полным доменным именем (FQDN). Эта операция выполняется при помощи одной команды. Через несколько секунд в браузере отобразится запущенное приложение.

![Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][qs-powershell-01]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.5 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать контейнер, нужно указать его имя, образ Docker и группу ресурсов Azure в командлете [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Либо же можно предоставить контейнер в Интернете с использованием метки DNS-имени.

Чтобы запустить контейнер Nano Server со службами IIS, выполните приведенную ниже команду. Значение `-DnsNameLabel` должно быть уникальным в пределах региона Azure, в котором создан экземпляр. Возможно, потребуется изменить это значение, чтобы гарантировать уникальность.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Через несколько секунд вы должны получить ответ на запрос. Контейнер изначально находится в состоянии **Создание**, но через несколько минут он запустится. Можно проверить состояние развертывания с помощью командлета [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Состояние подготовки, полное доменное имя (FQDN) и IP-адрес контейнера отображаются в выходных данных командлета:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Как только для **ProvisioningState** контейнера будет установлено значение `Succeeded`, перейдите к его `Fqdn` в браузере:

![Службы IIS, развернутые с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][qs-powershell-01]

## <a name="delete-the-container"></a>Удаление контейнера

По завершении работы с контейнером его можно удалить с помощью командлета [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы запустили готовый контейнер Windows в службе "Экземпляры контейнеров Azure". Если вы хотите выполнить сборку самостоятельно и развернуть контейнер в службе "Экземпляры контейнеров Azure" с помощью реестра контейнеров Azure, продолжайте изучение руководств по этой службе.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
