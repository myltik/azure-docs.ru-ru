---
title: Краткое руководство. Создание частного реестра Docker в Azure с помощью PowerShell
description: Быстрый способ изучить создание частного реестра контейнеров Docker в Azure с помощью Azure PowerShell.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885125"
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Краткое руководство. Создание Реестра контейнеров Azure с помощью PowerShell

Реестр контейнеров Azure — это управляемая служба частного реестра контейнеров Docker для создания, хранения и обслуживания образов контейнеров Docker. В этом руководстве рассматривается создание реестра контейнеров Azure с помощью PowerShell. Создав реестр, вы отправите в него образ контейнера, а затем развернете контейнер из реестра в службу "Экземпляры контейнеров Azure" (ACI).

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством требуется модуль Azure PowerShell 5.7.0 или более поздней версии. Выполните команду `Get-Module -ListAvailable AzureRM`, чтобы определить установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

Также необходим локально установленный модуль Docker. Docker предоставляет пакеты для систем [macOS][docker-mac], [Windows][docker-windows] и [Linux][docker-linux].

Та как в службе Azure Cloud Shell нет всех необходимых компонентов Docker (управляющая программа `dockerd`), ее нельзя использовать в этом руководстве.

## <a name="sign-in-to-azure"></a>Вход в Azure

С помощью команды [Connect-AzureRmAccount][Connect-AzureRmAccount] войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

После регистрации в Azure создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Создание реестра контейнеров

Теперь создайте реестр контейнеров в новой группе ресурсов с помощью команды [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В следующем примере создается реестр с именем myContainerRegistry007. Замените имя *myContainerRegistry007* в следующей команде. Затем с ее помощью создайте реестр.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Вход в раздел реестра

Перед отправкой и извлечением образов контейнеров необходимо войти в реестр. Воспользуйтесь командой [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential], чтобы сначала получить учетные данные администратора для реестра.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Затем выполните команду [docker login][docker-login], чтобы войти в систему.

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

После успешного входа возвращается сообщение `Login Succeeded`.

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Отправка образа в реестр

Теперь после входа в реестр можно отправить в него образы контейнера. Чтобы получить образ для отправки в реестр, извлеките общедоступный образ [aci-helloworld][aci-helloworld-image] из Docker Hub. Образ [aci-helloworld][aci-helloworld-github] представляет собой небольшое приложение Node.js, обслуживающее статические HTML-страницы, на которых отображается логотип службы "Экземпляры контейнеров Azure".

```powershell
docker pull microsoft/aci-helloworld
```

Выходные данные после извлечения образа аналогичны приведенным ниже.

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Прежде чем отправить образ в реестр контейнеров Azure, нужно добавить в него тег с полным доменным именем (FQDN) своего реестра. FQDN реестров контейнеров Azure имеет следующий формат: *\<имя реестра\>.azurecr.io*.

Заполните переменную, указав тег полного образа. Включите сервер входа, имя репозитория (aci-helloworld) и версию (v1) образа:

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

Теперь добавьте тег для образа с помощью команды [docker tag][docker-tag].

```powershell
docker tag microsoft/aci-helloworld $image
```

И наконец, отправьте образ в реестр с помощью команды [docker push][docker-push]:

```powershell
docker push $image
```

После того, как клиент Docker отправит образ, выходные данные должны быть аналогичны приведенным ниже.

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Поздравляем! Вы отправили первый образ контейнера в реестр.

## <a name="deploy-image-to-aci"></a>Развертывание образа в службе "Экземпляры контейнеров Azure"

После отправки образа в реестр разверните контейнер непосредственно в службе "Экземпляры контейнеров Azure", чтобы он выполнялся в Azure.

Сначала преобразуйте учетные данные реестра в объект *PSCredential*. Этот формат необходим команде `New-AzureRmContainerGroup`, которую вы используете для создания экземпляра контейнера.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Кроме того, метка имени DNS для вашего контейнера должна быть уникальной в пределах региона Azure, в котором он создан. Выполните следующую команду, чтобы заполнить переменную созданным именем:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Наконец, чтобы развернуть контейнер из образа из реестра контейнеров с одним ядром ЦП и 1 ГБ памяти, выполните команду [New-AzureRmContainerGroup][New-AzureRmContainerGroup]:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Вы получите начальный ответ от Azure с дополнительными сведениями о контейнере. Сначала он находится в состоянии ожидания.

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Чтобы отслеживать состояние контейнера и проверять, работает ли он, выполните команду [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] несколько раз. Запуск контейнера займет не больше минуты.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

В параметре ProvisioningState контейнера сначала было указано значение *Создание*, а затем после запуска состояние изменилось на *Успешно*.

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Просмотр выполняющегося приложения

После успешного развертывания в ACI, а также настройки и запуска контейнера перейдите к его полному доменному имени в браузере, чтобы просмотреть приложения, выполняющиеся в Azure.

Получите FQDN для контейнера с помощью команды [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

Выходными данными команды является полное доменное имя экземпляра контейнера:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Перейдите к нему в своем браузере:

![Приложение Hello World в браузере][qs-psh-01-running-app]

Поздравляем! У вас есть контейнер, запускающий приложение в Azure, развернутое непосредственно из образа контейнера в вашем частном реестре контейнеров Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с ресурсами, созданными в этом кратком руководстве, с помощью команды [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] вы можете удалить группу ресурсов, реестр контейнеров и экземпляр контейнера.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали реестр контейнеров Azure с использованием Azure CLI и запустили его экземпляр в службе "Экземпляры контейнеров Azure". Чтобы подробнее рассмотреть службу "Экземпляры контейнеров Azure", перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
