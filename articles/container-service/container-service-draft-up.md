---
title: "Использование черновика со Службой контейнеров Azure и реестром контейнеров Azure | Документация Майкрософт"
description: "Создайте кластер ACS Kubernetes и реестр контейнеров Azure, чтобы создать свое первое приложение в Azure с помощью черновика."
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, черновик, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dc3ae52b1ec6717c7e19a160e3e7ea5d211f1f5f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017



---

<a id="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes" class="xliff"></a>

# Использование черновика со Службой контейнеров Azure и реестром контейнеров Azure для создания и развертывания приложения в Kubernetes

[Черновик](https://aka.ms/draft) — новое средство с открытым исходным кодом, которое упрощает разработку приложений на основе контейнера и их развертывание в кластерах Kubernetes. Чтобы использовать черновик, особые знания Docker и Kubernetes или их установка не требуются. Использование таких средств, как черновик, позволит вам и вашей команде сосредоточиться на создании приложения с помощью Kubernetes, не вникая в инфраструктуру.

Вы можете использовать черновик с любым реестром образов Docker и любым кластером Kubernetes, а также использовать его локально. В рамках этого руководства вы узнаете, как использовать службу ACS с Kubernetes, запись ACR и Azure DNS, чтобы создать динамичный конвейер разработки CI/CD с помощью черновика.


<a id="create-an-azure-container-registry" class="xliff"></a>

## Создание реестра контейнеров Azure
Вы можете легко [создать реестр контейнеров Azure](../container-registry/container-registry-get-started-azure-cli.md). Для этого вам нужно выполнить следующее:

1. Создайте группу ресурсов Azure, чтобы управлять реестром ACR и кластером Kubernetes в ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Создайте реестр образов ACR с помощью команды [az acr create](/cli/azure/acr#create).
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


<a id="create-an-azure-container-service-with-kubernetes" class="xliff"></a>

## Создание службы контейнеров Azure с помощью Kubernetes

Теперь вы готовы использовать команду [az acs create](/cli/azure/acs#create), чтобы создать кластер ACS, используя Kubernetes в качестве значения `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> Так как Kubernetes не является типом оркестратора по умолчанию, вам необходимо использовать переключатель `--orchestrator-type kubernetes`.

В случае успешного выполнения процедуры результат будет выглядеть примерно так:

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Теперь, когда у вас есть кластер, вы можете импортировать учетные данные с помощью команды [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Теперь у вас есть локальный файл конфигурации для кластера, который нужен Helm и черновику для выполнения работы.

<a id="install-and-configure-draft" class="xliff"></a>

## Установка и настройка черновика
Инструкции по установке черновика находятся в [репозитории черновика](https://github.com/Azure/draft/blob/master/docs/install.md). Они относительно просты, но требуют определенной настройки, так как от [Helm](https://aka.ms/helm) зависит создание и развертывание чарта Helm в кластере Kubernetes.

1. [Скачайте и установите Helm](https://aka.ms/helm#install).
2. Используйте Helm для поиска и установки `stable/traefik` и входящий контроллер, чтобы разрешить входящие запросы для сборок.
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    Теперь настройте контрольное значение для контроллера `ingress`, чтобы сохранить значение внешнего IP-адреса после развертывания. Этот IP-адрес будет [сопоставлен с доменом развертывания](#wire-up-deployment-domain) в следующем разделе.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    В этом случае внешний IP-адрес для домена развертывания — `13.64.108.240`. Теперь вы можете сопоставить свой домен с этим IP-адресом.

<a id="wire-up-deployment-domain" class="xliff"></a>

## Подключение домена развертывания

Черновик создает выпуск для каждого чарта Helm, который он создает, в каждом приложении, над которым вы работаете. Каждый выпуск получает автоматически сформированное имя, которое используется черновиком в качестве _поддомена_ поверх _корневого домена развертывания_, которым вы управляете. (В этом примере в качестве домена развертывания мы используем `squillace.io`.) Чтобы включить этот режим поддомена, вы должны создать запись А для `'*'` в записях DNS для домена развертывания, чтобы каждый автоматически сформированный поддомен направлялся во входящий контроллер кластера Kubernetes.

Поставщики домена самостоятельно назначают DNS-серверы. Чтобы [делегировать назначение имен серверов домена службе Azure DNS](../dns/dns-delegate-domain-azure-dns.md), сделайте следующее:

1. Создайте группу ресурсов для зоны.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Создайте зону DNS для домена.
Используйте команду [az network dns zone create](/cli/azure/network/dns/zone#create), чтобы получить имена серверов для делегирования элемента управления DNS службе Azure DNS для домена.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Добавьте полученные DNS-серверы поставщику домена для домена развертывания, чтобы использовать Azure DNS для повторного указания домена.
4. Создайте запись набора записей A для домена развертывания, сопоставленного с IP-адресом `ingress` на шаге 2 в разделе выше.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
Результат будет выглядеть примерно так:
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Настройте черновик, чтобы использовать реестр и создать поддомены для каждого чарта Helm, который он создает. Чтобы настроить черновик, вам потребуется следующее:
  - имя реестра контейнеров Azure (в этом примере — `draft`);
  - раздел реестра или пароль, полученный из `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`;
  - корневой домен развертывания, который вы настроили для сопоставления с входящим внешним IP-адресом Kubernetes (в этом примере — `squillace.io`).

  Вызовите `draft init`, и процесс настройки запросит значения, приведенные выше. При первом запуске процесс будет выглядеть приблизительно так:
    ```
    draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

Теперь все готово для развертывания приложения.


<a id="build-and-deploy-an-application" class="xliff"></a>

## Сборка и развертывание приложения

В репозитории черновика находятся [шесть простых примеров приложений](https://github.com/Azure/draft/tree/master/examples). Клонируйте репозиторий и используйте [пример на языке Python](https://github.com/Azure/draft/tree/master/examples/python). Измените каталог examples/Python и введите `draft create`, чтобы создать приложение. Результат должен выглядеть примерно так:
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

Выходные данные будут содержать файл Dockerfile и чарт Helm. Чтобы выполнить сборку и развертывание, введите `draft up`. Результат будет обширным, но начальные выходные данные будут выглядеть примерно так:
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

Конечные выходные данные должны выглядеть примерно так:
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

С любым именем чарта вы можете использовать `curl http://gangly-bronco.squillace.io`, чтобы получить ответ `Hello World!`.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Теперь, когда у вас есть кластер ACS Kubernetes, вы можете использовать [реестр контейнеров Azure](../container-registry/container-registry-intro.md), чтобы создать больше различных развертываний этого сценария. Например, вы можете создать набор записей DNS домена draft._basedomain.toplevel_, с помощью которого можно отключать функции важного поддомена для определенных развертываний ACS.







