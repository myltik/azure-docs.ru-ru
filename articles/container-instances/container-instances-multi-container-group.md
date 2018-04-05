---
title: Развертывание многоконтейнерных групп в службе "Экземпляры контейнеров Azure"
description: Узнайте, как развернуть группу с несколькими контейнерами в службе "Экземпляры контейнеров Azure".
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 58fd4c18df5ec0a5d02be0e6e89cb2b4af26b20e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-a-container-group"></a>Развертывание группы контейнеров

Служба "Экземпляры контейнеров Azure" поддерживает развертывание нескольких контейнеров в одном узле с использованием [группы контейнеров](container-instances-container-groups.md). Это полезно при создании сопроводительного приложения для ведения журнала, мониторинга или любой другой конфигурации, когда службе требуется еще один прикрепленный процесс.

В этом документе рассматривается запуск простой конфигурации многоконтейнерного сопроводительного приложения. При этом развертывается шаблон Azure Resource Manager.

> [!NOTE]
> Многоконтенерные группы сейчас ограничены контейнерами Linux. Мы работаем над тем, чтобы обеспечить все функции для контейнеров Windows, но для текущей платформы есть отличия в [квотах и доступности регионов для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md).

## <a name="configure-the-template"></a>Настройка шаблона

Создайте файл с именем `azuredeploy.json` и скопируйте в него приведенный ниже код JSON.

В этом примере определяется группа контейнеров с двумя контейнерами, общедоступным IP-адресом и двумя предоставленными портами. Первый контейнер в группе запускает приложение с выходом в Интернет. Второй контейнер (сопроводительный) осуществляет HTTP-запрос к основному веб-приложению через локальную сеть группы.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
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
      "apiVersion": "2018-04-01",
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
                },
                {
                  "port": 8080
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
            },
            {
                "protocol": "tcp",
                "port": "8080"
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

Чтобы использовать частный реестр образов контейнеров, добавьте объект в документ JSON в следующем формате:

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

Разверните шаблон с помощью команды [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --name myContainerGroup --template-file azuredeploy.json
```

В течение нескольких секунд вы должны получить исходный ответ Azure.

## <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, используйте команду [az container show][az-container-show]. Она возвращает подготовленный общедоступный IP-адрес, по которому можно получить доступ к приложению.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Выходные данные:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Просмотр журналов

Просмотрите выходные данные журнала контейнера с помощью команды [az container logs][az-container-logs]. Аргумент `--container-name` определяет контейнер, из которого извлекаются журналы. В этом примере указывается первый контейнер.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Выходные данные:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Чтобы просмотреть журналы для сопроводительного контейнера, выполните ту же команду, указав имя второго контейнера.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Выходные данные:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

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
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Как видите, сопроводительное приложение периодически выполняет HTTP-запрос к основному веб-приложению через локальную сеть группы, чтобы убедиться, что оно работает. Пример сопроводительного приложения можно расширить, реализовав активацию оповещения при получении кода ответа HTTP, отличающегося от 200 OK.

## <a name="next-steps"></a>Дополнительная информация

В этой статье описаны шаги по развертыванию многоконтейнерного экземпляра контейнера Azure. Дополнительные сведения о службе "Экземпляры контейнеров Azure" см. в соответствующем руководстве.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
