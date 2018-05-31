---
title: Использование черновика со службой контейнеров Azure и реестром контейнеров Azure
description: Создайте кластер ACS Kubernetes и реестр контейнеров Azure, чтобы создать свое первое приложение в Azure с помощью черновика.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 68ad44bae0856ff000f2847049a15a946d83c0a3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32168543"
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Использование черновика со Службой контейнеров Azure и реестром контейнеров Azure для создания и развертывания приложения в Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Черновик](https://aka.ms/draft) — новое средство с открытым исходным кодом, которое упрощает разработку приложений на основе контейнера и их развертывание в кластерах Kubernetes. Чтобы использовать черновик, особые знания Docker и Kubernetes или их установка не требуются. Использование таких средств, как черновик, позволит вам и вашей команде сосредоточиться на создании приложения с помощью Kubernetes, не вникая в инфраструктуру.

Вы можете использовать черновик с любым реестром образов Docker и любым кластером Kubernetes, а также использовать его локально. В этом руководстве показано, как использовать ACS с Kubernetes и ACR для создания динамического защищенного конвейера разработчика в Kubernetes с помощью черновика и как использовать службу DNS Azure для предоставления доступа к этому конвейеру разработчика в домене для других пользователей.


## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure
Вы можете легко [создать реестр контейнеров Azure](../../container-registry/container-registry-get-started-azure-cli.md). Для этого вам нужно выполнить следующее:

1. Создайте группу ресурсов Azure, чтобы управлять реестром ACR и кластером Kubernetes в ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Создайте образ реестра ACR с помощью команды [az acr create](/cli/azure/acr#az_acr_create) и убедитесь, что для параметра `--admin-enabled` установлено значение `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Создание службы контейнеров Azure с помощью Kubernetes

Теперь вы готовы использовать команду [az acs create](/cli/azure/acs#az_acs_create), чтобы создать кластер ACS, используя Kubernetes в качестве значения `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
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

## <a name="install-and-configure-draft"></a>Установка и настройка черновика


1. Скачайте черновик для своей среды в https://github.com/Azure/draft/releases и установите его в пути, чтобы можно было использовать команду.
2. Скачайте Helm для своей среды в https://github.com/kubernetes/helm/releases и [, установите ее в свой путь, чтобы можно было использовать команду ](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Настройте черновик, чтобы использовать реестр и создать поддомены для каждого чарта Helm, который он создает. Чтобы настроить черновик, вам потребуется следующее:
  - имя реестра контейнеров Azure (в этом примере — `draftacsdemo`);
  - раздел реестра или пароль, полученный из `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`;

  Вызовите `draft init`, и в процессе настройки вам будет предложено задать значения, указанные выше. Обратите внимание, что URL-адрес реестра состоит из имени реестра (в этом примере `draftacsdemo`) и `.azurecr.io`. Ваше имя пользователя является именем реестра. При первом запуске процесс будет выглядеть приблизительно так:
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

Теперь все готово для развертывания приложения.


## <a name="build-and-deploy-an-application"></a>Сборка и развертывание приложения

В репозитории черновика находятся [шесть простых примеров приложений](https://github.com/Azure/draft/tree/master/examples). Клонируйте репозиторий и используйте [пример на языке Java](https://github.com/Azure/draft/tree/master/examples/java). Измените каталог examples/java и введите `draft create`, чтобы создать приложение. Результат должен выглядеть примерно так:
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Выходные данные будут содержать файл Dockerfile и чарт Helm. Чтобы выполнить сборку и развертывание, введите `draft up`. Выходные данные будут расширенными. Они должны выглядеть примерно так:
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Безопасный просмотр приложения

Теперь контейнер запущен в ACS. Чтобы просмотреть его локально, используйте команду `draft connect`, создающую безопасное подключение к IP-адресу кластера с конкретным портом для приложения. В случае успешного выполнения найдите URL-адрес, чтобы подключиться к приложению, в первой строке после индикатора **ВЫПОЛНЕНО**.

> [!NOTE]
> Если появится сообщение о том, что готовые модули отсутствуют, подождите некоторое время и повторите попытку или отследите готовность модулей с помощью `kubectl get pods -w` и повторите попытку.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

В предыдущем примере можно ввести `curl -s http://localhost:46143`, чтобы получить ответ `Hello World, I'm Java!`. Если нажать клавиши CTRL+C или CMD+C (в зависимости от среды ОС), защищенный туннель отключится и можно будет продолжить итерацию.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Предоставление доступа к приложению путем настройки домена развертывания с помощью службы Azure DNS

На предыдущих шагах вы уже выполнили цикл итерации разработчика, который создает черновик. Чтобы совместно использовать приложения в Интернете, сделайте следующее:
1. Установите входящий контроллер в кластере ACS (для предоставления общедоступного IP-адреса, в котором будет отображаться приложение).
2. Делегируйте личный домен Azure DNS и сопоставьте ваш домен с IP-адресом присваивания ACS вашему входящему контроллеру.

### <a name="use-helm-to-install-the-ingress-controller"></a>Используйте Helm для установки входящего контроллера.
Используйте **Helm** для поиска и установки `stable/traefik` и входящего контроллера, чтобы разрешить входящие запросы для сборок.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Теперь настройте контрольное значение для контроллера `ingress`, чтобы сохранить значение внешнего IP-адреса после развертывания. Этот IP-адрес будет [сопоставлен с доменом развертывания](#wire-up-deployment-domain) в следующем разделе.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

В этом случае внешний IP-адрес для домена развертывания — `13.64.108.240`. Теперь вы можете сопоставить свой домен с этим IP-адресом.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Сопоставление пользовательского поддомена с входящим IP-адресом

Черновик создает выпуск для каждого чарта Helm, который он создает, в каждом приложении, над которым вы работаете. Каждый выпуск получает автоматически сформированное имя, которое используется **черновиком** в качестве _поддомена_ поверх корневого _домена развертывания_, которым вы управляете. (В этом примере в качестве домена развертывания мы используем `squillace.io`.) Чтобы включить этот режим поддомена, вы должны создать запись А для `'*.draft'` в записях DNS для домена развертывания, чтобы каждый автоматически сформированный поддомен направлялся во входящий контроллер кластера Kubernetes. 

Поставщики домена самостоятельно назначают DNS-серверы. Чтобы [делегировать назначение имен серверов домена службе Azure DNS](../../dns/dns-delegate-domain-azure-dns.md), сделайте следующее:

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
Используйте команду [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create), чтобы получить имена серверов для делегирования элемента управления DNS службе Azure DNS для домена.
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
3. Добавьте полученные DNS-серверы поставщику домена для домена развертывания, чтобы использовать Azure DNS для повторного указания домена. Способы выполнения зависят от домена, предоставленного пользователем. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). 
4. После делегирования домена в службу Azure DNS создайте запись набора записей A для домена развертывания, сопоставленного с IP-адресом `ingress` на шаге 2 в разделе выше.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
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
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Переустановите **черновик**.

   1. Удалите **draftd** из кластера, введя `helm delete --purge draft`. 
   2. Переустановите **черновик** с помощью той же команды `draft-init`, но с параметром `--ingress-enabled`:
    ```bash
    draft init --ingress-enabled
    ```
   Ответьте на запросы, как описано выше. Однако есть еще один вопрос, на который нужно ответить, используя полный путь домена, настроенный с помощью Azure DNS.

6. Введите домен верхнего уровня для входящих сообщений (например, draft.example.com): draft.squillace.io
7. В этот раз при вызове `draft up` отобразится приложение (или `curl`) в URL-адресе в формате `<appname>.draft.<domain>.<top-level-domain>`. В этом случае `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Дополнительная информация

Теперь, когда у вас есть кластер ACS Kubernetes, вы можете использовать [реестр контейнеров Azure](../../container-registry/container-registry-intro.md), чтобы создать больше различных развертываний этого сценария. Например, вы можете создать набор записей DNS домена draft._basedomain.toplevel_, с помощью которого можно отключать функции важного поддомена для определенных развертываний ACS.






