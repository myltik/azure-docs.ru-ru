---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Развертывание приложения"
description: "Учебник по Azure экземпляры контейнером часть 3 из 3 — развертывание приложения"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 471caa1b24dc7017c70782c072b2068f9635244b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Развертывание контейнера в службе "Экземпляры контейнеров Azure"

Это последний учебник в трех выпусков. Ранее в этой серии, [был создан образ контейнера](container-instances-tutorial-prepare-app.md) и [помещено в реестре контейнеров Azure](container-instances-tutorial-prepare-acr.md). В этой статье завершает учебник рядов, развернув контейнер для экземпляров контейнера Azure.

Изучив это руководство, вы:

> [!div class="checklist"]
> * Разверните контейнер из реестра контейнера Azure с помощью Azure CLI
> * Откройте приложение в браузере
> * Просмотр журналов контейнера

## <a name="before-you-begin"></a>Перед началом работы

Учебником, что вы используете Azure CLI версии 2.0.23 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить см. в разделе [установить CLI Azure 2.0][azure-cli-install].

Для работы с этим учебником требуется Docker среды разработки, установленные локально. Docker содержит пакеты, которые легко настроить Docker для какого-либо [Mac][docker-mac], [Windows][docker-windows], или [Linux] [ docker-linux] системы.

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Среда разработки Azure CLI и Docker необходимо установить на локальном компьютере для выполнения заданий данного учебника.

## <a name="deploy-the-container-using-the-azure-cli"></a>Развертывание контейнера с помощью Azure CLI

Azure CLI позволяет одной командой развернуть контейнер в службе "Экземпляры контейнеров Azure". Так как образ контейнера размещается в частном реестре контейнеров Azure, необходимо включить учетные данные, необходимые для доступа к нему. Получите учетные данные с помощью следующих команд Azure CLI.

Сервер входа в реестр контейнеров (укажите используемое имя реестра):

```azurecli
az acr show --name <acrName> --query loginServer
```

Пароль для реестра контейнеров:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Чтобы развернуть образ контейнера из реестра контейнеров с запросом ресурсов (одно ядро ЦП и 1 ГБ памяти), выполните следующую команду. Замените `<acrLoginServer>` и `<acrPassword>` значениями, полученными посредством предыдущих двух команд.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80
```

В течение нескольких секунд вы получите исходный ответ Azure Resource Manager. Чтобы просмотреть состояние развертывания, используйте [Показать контейнера az][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Повторите [Показать контейнера az] [ az-container-show] команды, пока состояние изменится с *ожидающие* для *под управлением*, который займет менее чем за минуту. Если контейнер находится в состоянии *Выполняется*, перейдите к следующему шагу.

## <a name="view-the-application-and-container-logs"></a>Просмотр приложения и журналов контейнера

После успешного развертывания, отображения общедоступный IP-адрес контейнера с [Показать контейнера az] [ az-container-show] команды:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.ip
```

Пример выходных данных: `"13.88.176.27"`

Чтобы увидеть работающее приложение, перейдите по общедоступному IP-адресу в своем браузере.

![Приложение Hello World в браузере][aci-app-browser]

Также можно просмотреть выходные данные журнала контейнера:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Выходные данные:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если больше не требуется, ни один из ресурсов, созданных в данной серии учебных курсов, можно выполнить [удаление группы az] [ az-group-delete] команду, чтобы удалить группу ресурсов и все ресурсы, которые он содержит. Эта команда удаляет созданный вами реестр контейнеров, а также запущенный контейнер и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы завершили процесс развертывания контейнеров в службе "Экземпляры контейнеров Azure". Были выполнены следующие действия:

> [!div class="checklist"]
> * Развернуть контейнер из реестра контейнера Azure с помощью Azure CLI
> * Просмотреть приложения в браузере
> * Просмотреть журналы контейнера

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md