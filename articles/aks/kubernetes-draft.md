---
title: Использование Draft с AKS и реестром контейнеров Azure
description: Использование Draft с AKS и реестром контейнеров Azure
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5dfecefb6ce1d74e02c64371a864a6d3b07a2e1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257321"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Использование Draft со службой Azure Kubernetes (AKS)

Draft — это инструмент с открытым кодом, помогающий создавать и развертывать контейнеры в кластере Kubernetes, что позволяет больше сосредоточиться на цикле разработки, то есть на "внутреннем цикле" усиленной разработки. Draft работает по мере разработки кода, но перед фиксацией в системе управления версиями. С помощью Draft можно быстро повторно развернуть приложение в Kubernetes при изменении кода. Дополнительные сведения о Draft см. в [документации по Draft на сайте GitHub][draft-documentation].

В этом документе описано использование Draft с кластером Kubernetes в AKS.

## <a name="prerequisites"></a>предварительным требованиям

В действиях, описанных в этом документе, предполагается, что кластер AKS создан и с ним установлено соединение kubectl. Если вам требуются эти компоненты, прочитайте статью [Развертывание кластера Службы контейнеров Azure (AKS)][aks-quickstart].

Кроме того, необходим частный реестр Docker в реестре контейнеров Azure(ACR). Инструкции по развертыванию экземпляра ACR см. в статье [Создание реестра контейнеров с помощью Azure CLI][acr-quickstart].

Также в кластере AKS нужно установить Helm. Сведения об установке Helm см. в статье [Использование Helm со Службой контейнеров Azure (AKS)][aks-helm].

Наконец, необходимо установить [Docker](https://www.docker.com).

## <a name="install-draft"></a>Установка Draft

CLI Draft — это клиент, который выполняется в системе разработки и позволяет быстро развертывать код в кластер Kubernetes.

> [!NOTE]
> При установке Draft до версии 0.12 следует сначала удалить Draft из кластера с помощью `helm delete --purge draft`, а затем удалить локальную конфигурацию, выполнив `rm -rf ~/.draft`. В MacOS выполните `brew upgrade draft`.

Чтобы установить интерфейс командной строки Draft на компьютере Mac, выполните команду `brew`. Дополнительные параметры установки см. в [руководстве по установке Draft][draft-documentation].

```console
brew tap azure/draft
brew install draft
```

Теперь инициализируйте Draft с помощью команды `draft init`.

```console
draft init
```

## <a name="configure-draft"></a>Настройка Draft

Draft создает образы контейнеров локально, а затем развертывает их из локального реестра (в случае Minikube). Вы можете также самостоятельно указать реестр образов для использования. Этот пример использует реестр контейнеров Azure (ACR), поэтому необходимо установить отношение доверия между кластером AKS и реестром ACR и настроить Draft для отправки контейнера в ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Создание отношения доверия между кластером AKS и ACR

Чтобы установить отношения доверия между кластером AKS и реестром ACR, следует изменить субъект-службу Azure Active Directory, используемый для AKS, добавив в кластер роль участника с областью репозитория ACR. Для этого выполните приведенные ниже команды, заменив _&lt;aks-rg-name&gt;_ и _&lt;aks-cluster-name&gt;_ именем группы ресурсов и именем кластера AKS, а _&lt;acr-rg-name&gt;_ и  _&lt;acr-repo-name&gt;_ — именем группы ресурсов и именем репозитория ACR, с которым необходимо создать отношение доверия.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Эти действия и другие механизмы аутентификации для доступа к ACR выполняются при [аутентификации в ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Настройка Draft для отправки и развертывания из ACR

Теперь, когда установлено отношение доверия между AKS и ACR, можно выполнить приведенные ниже действия, чтобы можно было использовать ACR из кластера AKS.
1. Задайте значение параметра Draft `registry`, выполнив `draft config set registry <registry name>.azurecr.io`, где _&lt;registry name&lt;_ — имя реестра ACR.
2. Войдите в реестр ACR, выполнив команду `az acr login -n <registry name>`.

Так как вы вошли в ACR локально и создали отношение доверия между AKS и ACR, то для отправки в ACR или получения из ACR в AKS не требуются пароли или секреты. Аутентификация выполняется на уровне Azure Resource Manager с помощью Azure Active Directory.

## <a name="run-an-application"></a>Запуск приложения

Репозиторий Draft включает несколько примеров приложений, который могут использоваться для демонстрации Draft. Создайте клон репозитория.

```console
git clone https://github.com/Azure/draft
```

Перейдите в каталог примеров Java.

```console
cd draft/examples/example-java/
```

Чтобы запустить процесс, используйте команду `draft create`. Эта команда создает артефакты, которые используются для запуска приложения в кластере Kubernetes. Эти элементы включают файл Dockerfile, чарт Helm и файл `draft.toml`, который является файлом конфигурации Draft.

```console
draft create
```

Выходные данные:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Чтобы запустить приложение в кластере Kubernetes, используйте команду `draft up`. Эта команда создает файл Docker, чтобы создать образ контейнера, отправляет образ в ACR и, наконец, устанавливает диаграмму Helm для запуска приложения в AKS.

При первом выполнении этой команды отправка и получение образа контейнера может занять некоторое время. После кэширования базовых уровней это время значительно сократится.

```console
draft up
```

Выходные данные:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Тестирование приложения

Чтобы проверить приложение, используйте команду `draft connect`. Это команда прокси-подключения к модулю Kubernetes, позволяющая безопасное локальное подключение. По завершении доступ к приложению можно получить через предоставленный URL-адрес.

В некоторых случаях скачивание образа контейнера и запуск приложения может занять несколько минут. Если при доступе к приложению появляется ошибка, повторите попытку подключения.

```console
draft connect
```

Выходные данные:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Теперь протестируйте приложение. Для этого перейдите по адресу http://localhost:46143 (порт для предыдущего примера может быть другим). После завершения тестирования приложения используйте команду `Control+C` для остановки прокси-подключения.

> [!NOTE]
> Можно также использовать команду `draft up --auto-connect`, чтобы создать и развернуть приложение и немедленно подключиться к первому запущенному контейнеру, чтобы еще больше ускорить цикл итераций.

## <a name="expose-application"></a>Предоставление приложения

При тестировании приложения в Kubernetes можно предоставить доступ к приложению через Интернет. Это можно сделать с помощью службы Kubernetes с типом [LoadBalancer][kubernetes-service-loadbalancer] или с помощью [входящего контроллера][kubernetes-ingress]. В этом документе описано использование службы Kubernetes.


Сначала пакет Draft необходимо обновить, чтобы указать, что служба с типом `LoadBalancer` может быть создана. Чтобы сделать это, необходимо обновить тип службы в файле `values.yaml`.

```console
vi charts/java/values.yaml
```

Найдите свойство `service.type` и обновите значение с `ClusterIP` на `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Выполните команду `draft up` для повторного запуска приложения.

```console
draft up
```

Возвращение общедоступного IP-адреса службы может занять несколько минут. Для мониторинга выполнения используйте команду `kubectl get service` с контрольным значением.

```console
kubectl get service -w
```

Изначально для параметра *EXTERNAL-IP* службы отображается состояние `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Как только для параметра "EXTERNAL-IP" состояние `pending` изменится на `IP address`, используйте команду `Control+C`, чтобы остановить процесс отслеживания kubectl.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Чтобы увидеть приложение, откройте в браузере внешний IP-адрес.

```console
curl 52.175.224.118
```

Выходные данные:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Выполнение итерации приложения

Теперь, когда Draft настроен, а приложение выполняется в Kubernetes, можно настроить итерацию кода. Каждый раз, когда необходимо протестировать обновленный код, выполните команду `draft up`, чтобы обновить выполняющееся приложение.

Например, обновление приложения Java Hello World.

```console
vi src/main/java/helloworld/Hello.java
```

Обновите текст Hello World.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Выполните команду `draft up --auto-connect`, чтобы повторно развернуть приложение, как только pod будет готов отвечать.

```console
draft up --auto-connect
```

Выходные данные

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Наконец просмотрите приложение, чтобы просмотреть обновления.

```console
curl 52.175.224.118
```

Выходные данные:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Draft см. в документации Draft на GitHub.

> [!div class="nextstepaction"]
> [Документация по Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
