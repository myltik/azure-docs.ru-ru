---
title: "Создание первого контейнера в службе \"Экземпляры контейнеров Azure\" | Документация Azure"
description: "Начало работы со службой \"Экземпляры контейнеров Azure\" и развертывание в ней"
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Создание первого контейнера в службе "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" упрощает создание контейнеров и управление ими в Azure. В этом кратком руководстве мы создадим контейнер в Azure и предоставим к нему доступ в Интернете по общедоступному IP-адресу. Эта операция выполняется при помощи одной команды. Через несколько секунд контейнер отобразится в браузере:

![Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][aci-app-browser]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Команды CLI для службы "Экземпляры контейнеров Azure" сейчас доступны только в Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Служба "Экземпляры контейнеров Azure" является ресурсом Azure и должна быть помещена в группу ресурсов Azure — логическую коллекцию, в которой выполняется развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать контейнер, нужно указать его имя, образ Docker и группу ресурсов Azure. Либо же можно предоставить контейнер в Интернете по общедоступному IP-адресу. В этом случае мы используем контейнер, в котором размещено очень простое веб-приложение, написанное на [Node.js](http://nodejs.org).

Команды CLI для службы "Экземпляры контейнеров Azure" сейчас доступны только в Azure Cloud Shell.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

Через несколько секунд вы должны получить ответ на запрос. Изначально контейнер будет в состоянии **создания**, но через несколько секунд он запустится. Его состояние можно проверить с помощью команды `show`:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

В нижней части окна выходных данных отобразятся сведения о состоянии подготовки контейнера и его IP-адрес:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Когда контейнер перейдет в состояние **успешного выполнения**, к нему можно будет получить доступ в браузере по указанному IP-адресу. 

![Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][aci-app-browser]

## <a name="pull-the-container-logs"></a>Извлечение журналов контейнера

Вы можете извлечь журналы для созданного контейнера с помощью команды `logs`:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Выходные данные:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Удаление контейнера

По завершении работы с контейнером его можно удалить с помощью команды `delete`:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Полный код для контейнера, используемого в этом кратком руководстве, и файл Dockerfile доступны [на сайте GitHub][app-github-repo]. Если вы хотите выполнить сборку самостоятельно и развернуть контейнер в службе "Экземпляры контейнеров Azure" с помощью реестра контейнеров Azure, продолжайте изучение руководств по этой службе.

> [!div class="nextstepaction"]
> [Руководства по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
