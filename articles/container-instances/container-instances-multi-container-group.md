---
title: "Развертывание многоконтейнерных групп в службе \"Экземпляры контейнеров Azure\""
description: "Узнайте, как развернуть группу с несколькими контейнерами в службе \"Экземпляры контейнеров Azure\"."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5de818b0b47ee3345ddbc41455f5e953c5b96aa4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="deploy-a-container-group"></a>Развертывание группы контейнеров

Экземпляры контейнером Azure поддерживает развертывание несколько контейнеров на одном узле с помощью *группы контейнеров*. Это полезно при создании сопроводительного приложения для ведения журнала, мониторинга или любой другой конфигурации, когда службе требуется еще один прикрепленный процесс.

В этом документе описывается выполнение простого сопроводительные несколькими контейнера конфигурации путем развертывания шаблона диспетчера ресурсов Azure.

## <a name="configure-the-template"></a>Настройка шаблона

Создайте файл с именем `azuredeploy.json` и скопируйте в него следующий JSON.

В этом примере определяется группа контейнеров с двумя контейнерами и общедоступным IP-адресом. Первый контейнер в группе запускает приложение с выходом в Интернет. Второй контейнер (сопроводительный) осуществляет HTTP-запрос к основному веб-приложению через локальную сеть группы.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
        "location": "[resourceGroup().location]",
        "properties": {
          "containers": [
            {
              "name": "[variables('container1name')]",
              "properties": {
                "image": "[variables('container1image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                },
                "ports": [
                  {
                    "port": 80
                  }
                ]
              }
            },
            {
              "name": "[variables('container2name')]",
              "properties": {
                "image": "[variables('container2image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                }
              }
            }
          ],
          "osType": "Linux",
          "ipAddress": {
            "type": "Public",
            "ports": [
              {
                "protocol": "tcp",
                "port": "80"
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

Чтобы использовать образ контейнера закрытого реестра, добавьте объект документа JSON в следующем формате.

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Развертывание шаблона с [создания развертывания группы az] [ az-group-deployment-create] команды.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --name myContainerGroup --template-file azuredeploy.json
```

Через несколько секунд вы должны получить первого ответа из Azure.

## <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, используйте [Показать контейнера az] [ az-container-show] команды. Это возвращает подготовленных общедоступный IP-адрес, по которому можно получить доступ приложения.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Выходные данные:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Просмотр журналов

Просмотреть выходные данные журнала для контейнера с помощью [журналы контейнера az] [ az-container-logs] команды. Аргумент `--container-name` определяет контейнер, из которого извлекаются журналы. В этом примере указывается первый контейнер.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Выходные данные:

```bash
listening on port 80
::1 - - [18/Dec/2017:21:31:08 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [18/Dec/2017:21:31:11 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [18/Dec/2017:21:31:15 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Чтобы просмотреть журналы для сопроводительного контейнера, выполните ту же команду, указав имя второго контейнера.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Выходные данные:

```bash
Every 3s: curl -I http://localhost                          2017-12-18 23:19:34

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Mon, 18 Dec 2017 23:19:34 GMT
Connection: keep-alive
```

Как видите, сопроводительное приложение периодически выполняет HTTP-запрос к основному веб-приложению через локальную сеть группы, чтобы убедиться, что оно работает. Пример сопроводительного приложения можно расширить, реализовав активацию оповещения при получении кода ответа HTTP, отличающегося от 200 OK.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье описаны шаги, необходимые для развертывания экземпляра несколькими контейнера Azure. Для взаимодействия с начала до конца экземпляры контейнером Azure см. в учебнике экземпляров контейнера Azure.

> [!div class="nextstepaction"]
> [Учебник по Azure экземпляры контейнером][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
