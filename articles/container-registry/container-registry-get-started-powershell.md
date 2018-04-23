---
title: Краткое руководство. Создание частного реестра Docker в Azure с помощью PowerShell
description: Быстрый способ изучить создание частного реестра контейнеров Docker с помощью Azure PowerShell.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ae21fc7ab016071d075324bf813243cef58dcd04
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Краткое руководство. Создание Реестра контейнеров Azure с помощью PowerShell

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра реестра контейнеров Azure с помощью PowerShell, отправление образа контейнера в реестр и, наконец, развертывание контейнера из реестра в службе "Экземпляры контейнеров Azure" (ACI).

Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

Также необходим локально установленный модуль Docker. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Создайте экземпляр записи контроля доступа (ACR) с помощью команды [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В следующем примере используется имя *myContainerRegistry007*. Замените его уникальным значением.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Вход в ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Сначала воспользуйтесь командой [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential), чтобы получить учетные данные администратора для экземпляра ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Затем выполните команду [docker login][docker-login], чтобы войти в экземпляр ACR.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

По завершении команда возвращает `Login Succeeded`. Также может появиться предупреждение системы безопасности, рекомендующее использовать параметр `--password-stdin`. Хотя его использование выходит за рамки данной статьи, мы рекомендуем следовать данной рекомендации. Чтобы получить дополнительные сведения, ознакомьтесь с описанием команды [docker login][docker-login].

## <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. При необходимости выполните следующую команду, чтобы извлечь предварительно созданный образ из Docker Hub.

```powershell
docker pull microsoft/aci-helloworld
```

Образ должен быть снабжен тегом имени сервера для входа в ACR. Для этого используйте команду [docker tag][docker-tag].

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Наконец, воспользуйтесь командой [docker push][docker-push] для отправки образа в ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Развертывание образа в службе "Экземпляры контейнеров Azure"
Для развертывания образа в виде экземпляра контейнера в службе "Экземпляры контейнеров Azure" (ACI) сначала необходимо преобразовать учетные данные реестра в объект PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Чтобы развернуть образ контейнера из реестра контейнеров с одним ядром ЦП и 1 ГБ памяти, выполните следующую команду:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Вы получите начальный ответ от Azure Resource Manager с дополнительными сведениями о контейнере. Чтобы отслеживать состояние контейнера и проверять, работает ли он, повторите команду [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Операция займет не больше минуты.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Пример выходных данных: `Succeeded`

## <a name="view-the-application"></a>Просмотр приложения
После успешного развертывания в ACI получите общедоступный IP-адрес контейнера, используя команду [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup].

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Пример выходных данных: `"13.72.74.222"`

Чтобы увидеть работающее приложение, перейдите по общедоступному IP-адресу в своем браузере. Должно отобразиться примерно следующее:

![Приложение Hello World в браузере][qs-portal-15]

## <a name="clean-up-resources"></a>Очистка ресурсов

Ненужные группу ресурсов, реестр контейнеров Azure и все экземпляры контейнеров Azure можно удалить с помощью команды [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup].

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали реестр контейнеров Azure с использованием Azure CLI и запустили его экземпляр в службе "Экземпляры контейнеров Azure". Чтобы подробнее рассмотреть службу "Экземпляры контейнеров Azure", перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
