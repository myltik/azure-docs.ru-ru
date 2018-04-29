---
title: Краткое руководство. Создание частного реестра Docker в Azure с помощью Azure CLI
description: Быстрый способ изучить создание частного реестра контейнеров Docker с помощью Azure CLI.
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: ed5c2d0b925d92f318b36b36d060ebc923499ee5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>Краткое руководство. Создание реестра контейнеров с использованием Azure CLI

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра реестра контейнеров Azure с помощью Azure CLI, отправление образа контейнера в реестр и, наконец, развертывание контейнера из реестра в службе "Экземпляры контейнеров Azure" (ACI).

Для этого руководства требуется Azure CLI 2.0.27 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

Также необходим локально установленный модуль Docker. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом кратком руководстве вы создадите реестр уровня *Базовый*. Реестр контейнеров Azure доступен в нескольких номерах SKU, которые кратко описаны в следующей таблице. См. дополнительные сведения о [номерах SKU реестра контейнеров][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Создайте экземпляр ACR с помощью команды [az acr create][az-acr-create].

Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В следующем примере используется имя *myContainerRegistry007*. Замените его уникальным значением.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

При создании реестра выходные данные выглядят так:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

В дальнейшем в этом кратком руководстве будет использоваться `<acrName>` как заполнитель имени реестра контейнеров.

## <a name="log-in-to-acr"></a>Вход в ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Чтобы сделать это, используйте команду [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает сообщение `Login Succeeded`.

## <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. Если у вас еще нет образов локального контейнера, используйте следующую команду, чтобы извлечь существующий образ из Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Прежде чем отправить образ в реестр, нужно добавить в него тег с полным именем сервера входа для ACR. Выполните следующую команду, чтобы получить полное имя сервера входа для экземпляра ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Присвойте образу тег с помощью команды [docker tag][docker-tag]. Замените значение `<acrLoginServer>` именем сервера входа для экземпляра ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Наконец, воспользуйтесь командой [docker push][docker-push] для принудительной отправки образа в экземпляр ACR. Замените значение `<acrLoginServer>` именем сервера входа для экземпляра ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Список образов контейнеров

В следующем примере перечисляются репозитории в реестре:

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```bash
Result
----------------
aci-helloworld
```

В следующем примере перечисляются теги в репозитории **aci helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Выходные данные:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Развертывание образа в службе "Экземпляры контейнеров Azure"

Чтобы развернуть экземпляр контейнера из реестра, который вы создали, во время развертывания необходимо указать учетные данные реестра. В рабочих сценариях для доступа к реестру контейнеров следует использовать [субъект-службу][container-registry-auth-aci], но чтобы упростить это быстрое руководство, включите пользователя "admin" в реестре с помощью следующей команды:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

После того как пользователь "admin" будет включен, имя пользователя будет таким же, как и имя реестра, и вы сможете получить пароль с помощью следующей команды:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Чтобы развернуть образ контейнера с одним ядром ЦП и 1 ГБ памяти, выполните следующую команду: Замените `<acrName>`, `<acrLoginServer>` и `<acrPassword>` значениями, полученными посредством предыдущих команд.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Вы получите начальный ответ от Azure Resource Manager с дополнительными сведениями о контейнере. Чтобы отслеживать и проверять состояние контейнера и проверять, работает ли он, повторите команду [az container show][az-container-show]. Операция займет не больше минуты.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Просмотр приложения

После успешного развертывания в ACI получите FQDN контейнера с помощью команды [az container show][az-container-show].

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Пример выходных данных: `"aci-demo.eastus.azurecontainer.io"`

Чтобы увидеть работающее приложение, перейдите по общедоступному IP-адресу в своем браузере.

![Приложение Hello World в браузере][aci-app-browser]

## <a name="clean-up-resources"></a>Очистка ресурсов

Ненужные группу ресурсов, экземпляр ACR и все образы контейнеров можно удалить с помощью команды [az group delete][az-group-delete].

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали реестр контейнеров Azure с использованием Azure CLI, отправили образ контейнера в реестр и запустили его экземпляр в службе "Экземпляры контейнеров Azure". Чтобы подробнее рассмотреть службу "Экземпляры контейнеров Azure", перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az_container_show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md